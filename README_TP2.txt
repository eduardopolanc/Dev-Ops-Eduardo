2-1 What are testcontainers?
Those are docker containers used to simulate the interaction between our code and the entity it has to deal with.
For example if we are developing a feature to comunicate to an SQL database, the testcontainer would be
an sql database constructed with docker to test the interaction of our feature. after the testing is over
the container is removed.

2-2 For what purpose do we need to use secured variables ?
Because this way it is safetier to keep sensitive information hide from the exterior. When we create
GitHub secrets for example we assing a content that is just kept until the secret is open again,
ensuring the use of it without revealing what is in it.

2-3 Why did we put needs: build-and-test-backend on this job? Maybe try without this and you will see!
We put it to build dependency. With needs: what we do is adding execution order to out tasks. in this
case for example we are stating that the task of building the image is just executed after we test the code of creating the image

2-4 For what purpose do we need to push docker images?
Because this way they are accesible from the web, and we can keep them secure and we can reuse it later
if needed, out of our computer.