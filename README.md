# UseCaseOne

1. Provision an ec2 instance.
2. Provision jenkins in it.
3. Create a new repository in github.
4. create a pipeline in jenkins and configure it to read the changes in github repo.
5. As soon as a commit is made in github by updating a file named for eg, "Akshay.txt", it has to trigger the pipeline and run and deploy
nginx server in ec2 instance and once it is deployed, the server has to be accessed using route53.
6. If a commit is made using a file other than the file mentioned above, pipeline has to be triggered but it shouldn't perform any activity. 
(OR) deploy some other application (install any text editor)