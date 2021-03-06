# Reinforcement Learning using T3D algorithm

Goal here is to get the car to stay on road using Reinforcement Learning - T3D algorithm. 
I have taken a phased approach to this problem
 >- Phase 1 - Integrate T3D learning to the Kivy environment ; with sensor data using image patch brightness ( converting to scalar ) as the state dimension. Fully connected Actor and Critic models were used.Here idea was a get a understanding of the working of TD3. 

 >- Phase 2 - Replace the fully connected network in the above network with a **CNN** and feed the the image frames in front of the car(took it at 3 angles) as sensory data to it and **added orientation with destination** . The 3 images correspond to 
 >>- 1 20x20 in front of car
 >>- 1 20x20 to 30% left of car
 >>- 1 20x20 to 30% right of car

Describing both the phases below

## **Phase 1** 
  In this phase i have taken car environment and integrated it with the T3D learning. I have NOT used CNN in this phase.

  https://youtu.be/6NWjU9S8zro

  ### Parameters used
  >- **Action Dimension** : 1 - angle of rotation
  >- **State Dimension** : 5
  >>- 3 brightness parameter from sensor patches
  >>- 2 oreintations from target x,y coordinates
  >- Timesteps of taking action : 1000
  >- Episode **done** if
  >>- Max epsidoes steps reached : 1200
  >>- Car gets to the edges
  >>- Reaches the destination
  >- **Rewards**
  >>- On sand = - 1
  >>- Car hits the edges = - 1
  >>- If on Road and distance is reducing = + 1
  >>- On Road = + 0.8

  >- Network

  >>- Used fully connected layers for Actor and Critic Model

  >- Observations
  >>- Started driving on the road after 27 episodes




## **Phase 2**

  In this phase integrated it with the T3D learning and passed the sensory data as image frames to a CNN
  
 >- Final submission : With 3 patches in front of car + **2 orientation with destination** as input
 >>- https://youtu.be/TqYSj9yXtN4
   

  ### Parameters used
  >- **Action Dimension** : 1 - angle of rotation
  >- **State Dimension** : 1200 + 2
  >>- 3 channels of 20x20 = 1200 fed to the CNN + 2 orientation with destination (+ & -)

  >- Episode **done** if
  >>- Max epsidoes steps reached : 12000
  >>- Car gets to the edges
  >>- Reaches the destination
  
  >- **Rewards**
  >>- On sand = - 1
  >>- Car hits the edges = - 1
  >>- If on Road and distance is reducing = + 1
  >>- On Road = + 0.8

  >- Network

  >>- Convblk1 -> MaxPool -> ConvBlk -> GAP -> FC 









## Other attempts, debugging and observations

  >- Attempt 1 : With 3 image patches (3 x40x40) in front of car as sensory data only as input  to the CNN
  >>- https://youtu.be/Du_JzbbTuJw
  >- Attempt 2 : With 3 patches in front of car + **2 orientation with destination** as input (but i think something is wrong with way sensory data is fed - does not seem be factoring it)   
  >>- https://youtu.be/NW1GH8aQFas
  >- Attempt 3 : With 1 patch of 80x80 around the car + **2 orientation with destination** as input  
  >>- https://youtu.be/ixRxi3h5h1c (initial learning)
  >>- https://youtu.be/CQKzaTQ2n-I ( towards destination and close to road)

 ### **Other Issues**

    Car started to rotate after few episodes.Below is the diagnostics and steps taken. This could mean that only extreme angle (-max_action or + max_action) was predicted. This meant network was unstable. I could not be sure if this was for exploding gradients or vanishing gradients. Took the below approach



    >- Enabled training logs and looked at buffer values ; predicted rotations
    >- Compared training logs between run using T3D + FC + Car(successful) vs T3D + CNN + Car(un-successful)

    > Log : https://github.com/senthilva/RL_car_using_T3D/blob/master/logs/analysis_training_logs

            Target Q's should be negative ( they are low positive in CNN network)
              Current Q's should be negative ( they are low positive in CNN network)
              Critic loss should be low positive (they are high positive in CNN network)
              Action Loss should be large positive ( they are los positive in CNN network)
              Q1 should be large negative ( they are small negative in CNN network)

    >- Led me to realize i had not used Batch Normalization across layers. Enabled that and network improved.


    >-  Adding 2 additional state dim of + & -orientation with destination; helped with the training



