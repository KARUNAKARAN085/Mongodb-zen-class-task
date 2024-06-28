# MongoDB Task

# Design database for Zen class programme

## Tables

1. **users**
2. **codekata**
3. **attendance**
4. **topics**
5. **tasks**
6. **company_drives**
7. **mentors**

![alt text](image.png)

![alt text](image-1.png)

1. Find all the topics and tasks which are thought in the month of October 

    ```javascript
    db.topics.aggregate([
    {$lookup: {
      from: 'tasks',
      localField: 'topic_id',
      foreignField: 'task_id',
      as: 'taskinfo'
    }},
    {
      $match: {$and: [
      { topic_date: { $gte: new Date("2021–10–01"), $lt: new Date("2021–10–30") } },
      {$or: [
      { "taskinfo.due_date": { $gte: new Date("2021–10–01"), $lte: new Date("2021–10–30") } },
      { "taskinfo.due_date": { $exists: false } }]}]}},
      {
      $project: {
        _id: 0,
        topic_id: 1,
        topic: 1,
        topic_date: 1,
        due_date: "$taskinfo.due_date"
   }}]);
   ```

2. Find all the company drives which appeared between 15 oct-2021 and 31-oct-2021

    ```javascript
    db.company_drives.find({ 
      $or: [{ drive_date: 
        { $gte: new Date("2021-10-15"),
        $lte: new Date("2021-10-31")} 
      }]
    });
    ```

3. Find all the company drives and students who are appeared for the placement

    ```javascript
    > db.company_drives.aggregate([
      {
        $lookup: {
          from: "users",
          localField: "user-id",
          foreignField: "user-id",
          as: "info",
      },
    },
    {
      $project: {
        _id: 0,
        company_name: 1,
        drive_date: 1,
        "info.name": 1,
        "info.email": 1,
        "info.user-id": 1,
      },
    },
    ]);

4. Find the number of problems solved by the user in codekata

    ```javascript
    db.codekata.aggregate([
      {
        $lookup: {
        from: "users",
        localField: "user-id",
        foreignField: "user-id",
        as: "info",
        },
      },
      {
        $project: {
        _id: 0,
        no_of_problems_solved: 1,
        "info.name": 1,
        },
      },
    ]);
    ```

5. Find all the mentors with who has the mentee's count more than 15
   
    ```javascript
   db.mentors.find({mentee_count:{$gte:15}});
   ```

6. Find the number of users who are absent and task is not submitted between 15 oct-2021 and 31-oct-2021

    ```javascript
    db.attendence.aggregate([
      {
        $lookup: {
        from: "topics",
        localField: "topic_id",
        foreignField: "topic_id",
        as: "topics",
        },
      },
      {
        $lookup: {
          from: "tasks",
          localField: "topic_id",
          foreignField: "topic_id",
          as: "tasks",
        },
      },
      { $match: { $and: [{ staus: false }, { "tasks.submitted": false }] } },
      { $match: { 
        $and: [{
          $or: [
            { "topics.topic_date": { $gte: new Date("2021–10–15") , $lte: new Date("2021–10–31") } }
          ],
        },
        {
          $or: [
            { "tasks.due_date": { $gte: new Date("2021–10–15") , $lte: new Date("2021–10–31") } },
          ],
        },],
      },},
      {$count:"Absentees"}
    ]);
    ```