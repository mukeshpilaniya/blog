OAES Design Pattern Activity

**Design Problem:-** Let&#39;s suppose that for giving an exam a student has to go into the exam center and the exam starts at a specific time that is prior known to the student. Exam server also has a list of registered Students(Subscribers) and ExamServer sends question papers to register students screen automatically when the exam starts. So for all the students, server will not fetch question paper from database all the time, it will fetch only for one student and for rest of them it will create a clone of a question paper, so it will follow Prototype design Pattern and for sending Question Paper to registered student it will follow Observer design pattern.

**Design Pattern:-** observer design pattern while sending question Paper to registered Students, ProtoType design pattern while creating a clone of question Paper.

![](RackMultipart20201214-4-1thu7r8_html_800371f5f83eca8e.png)

Overview:-

- notifyResteredStudent()- method sent Question Paper to all registered Student who are appearing for exam
- Cloneable- Inbuilt java interface methi\od for cloning question question
