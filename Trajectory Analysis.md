There are 2 tasks in Trajectory analysis of rival UAVs:
1. Trajectory Prediction of a rival UAV
     In this task we try to predict the rival UAV's next direction or position by using previous given positions of the subject UAV (we can even attempt to predict the motion planning algorithms of that UAV by given positions ).
2. Trajectory tracking of a rival UAV
     In the second task our goal is to take our UAV as close as possible to the predicted position (The goal is to get behind the rival UAV) so our camera can detect rival UAV and locks on it. 


## Trajectory Prediction
In trajectory prediction there are many approaches available but I think we should concentrate on Machine learning based approach:
   Basically our task is a time sequence problem, so we should look for the deep learning models with best performance in these problems. My first recommendations are TCN models and transformers since they have best results in time sequence problems. Also some resource suggesting RNN and LSTM models too. Each of these models need to be examined and researched one by one to find their pros and cons.

## Trajectory tracking
After doing the prediction part as accurate as possible, tracking the rival UAV's path should be an autonomous task 


# Research questions
1. Can we make use of known curves like dubins path to estimate coordinates?
     I do not think that dubins path can be significantly helpful in trajectory prediction but probably it is going to be the most helpful tool to optimize the automation task in trajectory tracking (actually we are going to use dubins path to find the shortest route between our UAV and desired position)
2.  Can machine learning algorithms help us achieve better predictions?
     Most probably Deep learning models (especially TCNs and transformers) are going to be our main approach for prediction challenge, but we should do some experiments with different machine learning algorithms (regression, random forests ....) to see how can they perform in predicting task.
3. How can we generate the data needed to train a ML model?
     This question should be answered by Mr sezer. we always can use simulator to generate new data of UAV's motion and if the data from last year flights are available, that can be really helpful too.
4. How can we measure the performance of developed solutions systematically so we can keep track of our improvements?
     The ideal scenario would be a test flight( a real one and a simulated one) for each different solution we develop, But since that is not too logical, I think we should discuss and develop some different approaches (5 at most) for our task and develop them. After that we can use simulated environment and model testing scores to choose the best approach and try to refine it to achieve best results as possible.