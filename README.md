# Centralized-Control-algorithm-for-3-Unmanned-Aerial-Vehicles

As part of my dissertation, I created an algorithm allowing multiple Unmanned Aerial Vehicles (UAV) to cooperate between them in order to search a defined area. The programme was written using the modelling language VDM. The model is a swarm of UAVs under centralized control strategy. The UAVs initially, exchange information about their id number, position, and battery lifetime. Afterwards, they elect a leader UAV. The leader UAV is the one with the least id number. The leader calculates how many square metres is the overall area, and determines how many UAVs are required for the mission. The leader splits the area into sub-areas in the most efficient way, in order
to send tasks to the other UAVs. Using the ether project for communication exchange is not possible to send a message to a particular UAV, hence, the leader sends a tag id that indicates which UAV should undertake this task, and the coordinates of this task. The leader
broadcasts the same message until it receives an acknowledgment. When the worker UAV receives the task, it sends an acknowledgment to the leader. If the mission requires more UAVs, the leader follow the same process. We achieved to model a partially fault tolerance version of this model. The leader compares the information received from a UAV with the information received from the same UAV in
the previous iteration. If the information is the same, then the leader assigns a penalty point to that UAV, if the UAV reaches a certain number of penalty points, the leader erases that UAV from the list of the functional UAVs. If that UAV were undertaking a task, then the leader stores that task in a list, and assigns the task to the next available functional UAV. In this figure, the black line is the leader’s trajectory, the green line is the faulty UAV’s trajectory, and the blue line is the other UAV’s trajectory. In this point the faulty UAV crashes, and as the leader finished its task first, it undertook the faulty UAV’s task.

The following paragraphs are a part from my dissertation thesis explaining the model.

Centralized Control
Our next objective was to create a model of multiple UAVs searching an area under centralized control strategy. For this work, the swarm should cooperate successfully to elect one leader UAV so as to distribute the workload between the swarm effectively. 

Role
A new type ‘Role’ was created to distinguish the leader UAV from the others. A UAV can take only one role, either LEADER or WORKER. The operation SetRole is responsible for assigning the respective role to each UAV. The operation is functional in the INITIALIZATION mode. Similar to the previous project, after having exchanged information, the UAVs are able to determine how many UAVs are taking part in the mission, as well as their id numbers. The UAVs elect as leader the UAV with the least id number. We assume that the user gives different id numbers to each UAV in the multi-model configuration.

State
Moreover, there was a need to give different states to the leader UAV. Hence, another type called ‘State’ was created. The leader UAV can change between two states, CALCULATE and WAIT. In contrary to the previous projects, in this project only the leader UAV is responsible for the division and distribution of tasks. When the leader is in CALCULATE state, it calculates how many square metres is the overall area, and how many UAVs are required for the mission. Similar to the previous projects, the user inputs the variable which determines how many square metres are allowed for a single UAV to search alone. The user is able to change the value of this variable through the HardwareInterface.
 Afterwards, the leader divides the area into sub-areas in order to send them to the other members of the team. The leader UAV splits the area in the most efficient way such as the technique we described in the previous models.
After having determined a sub-area, the leader sends the coordinates to a worker UAV so as to carry this task out. Due to the fact that every UAV broadcasts messages to every other UAV and not to a particular one, a tag id number is required to be sent to ensure that the message will be received from the right UAV. The leader enters the WAIT state, broadcasting the message to every worker. As long as the leader is in this state it will continue broadcasting the same message until it receives an acknowledgment from the worker UAV indicating that it received the message.
The workers remain at their bases until they receive a message from the leader indicating their id number and coordinates of an area to search. If they receive such message, they send an acknowledgment to the leader that they received the message.
When the leader receives an acknowledgment, it enters the CALCULATE state again. If the mission requires more UAVs, the leader will repeat the process until the whole area is covered.
If the overall area is too big, the leader divides the area according to the number of the UAVs taking part in the mission. Each UAV undertakes an equal portion of the area. The division of the area is achieved as the method we used in the previous projects. 

Lists of Records
When the leader receives the acknowledgment that a worker has received its task, the leader stores the UAV’s id number and the coordinates of its task into a map (listOfWorkers). When the worker finishes its task, it broadcasts a message to the leader mentioning it. When the leader receives the message that a particular UAV has finished its task, it removes that UAV from the list. Moreover, the leader stores the id number of that UAV and its task’s coordinates into another map, so as to keep record of all the areas that have been covered. (listOfAchievers). The leader broadcasts the two lists that contain the id and tasks of those UAVs undertaking a task and those that have finished, so every UAV knows which one undertakes which task and which UAVs have finished their tasks.

Communication Exchange
In order to achieve centralized control strategy, the ether model was used for communication exchange. Each UAV is able to broadcast its id number, position, battery life, a tag Id, four real numbers, an acknowledgment, a map consisting of the id numbers of the UAVs undertaking a task and the coordinates of their task, and a map consisting of the UAVs that have finished their tasks and the coordinates of their tasks.
The id number and position are sent, allowing every member of the swarm to determine the other UAVs that are taking part in the mission and their position. 
The tag id and the four real numbers are used from the leader UAV when it sends a task to a WORKER UAV. The tag id indicates which UAV should undertake the task, and the four numbers form two pairs of coordinates indicating the sub-area that the worker UAV should cover. The worker UAV knows when a task is intended for itself when the tag id is equal to its id number.
The acknowledgment is used from the worker UAVs to send an acknowledgment to the leader that they received their task. After the completion of their task, the workers send the leader another acknowledgment indicating that they finished it. If the leader receives such an acknowledgment, it erases the worker UAV from the list of the UAVs undertaking a task, and stores the UAV and its task in the map consisting of the UAVs that have finished their tasks. The leader does the same when it finishes its task.
The two maps are being sent from the leader UAV to the other members of the team, allowing them to know which UAVs have been assigned a task, and which UAVs have finished their tasks and the areas covered so far. In this stage, these maps are not used for any particular reason, but give potentials for future work.

Co-simulation
We carried out simulations using two or three UAVs. The multi-model consisted of the 20-sim FMU, the VDM-RT FMU describing the controller, and the VDM-RT FMU representing the ether project. We also made use of the 20-sim model responsible for the 3D animation to visualise the movement of the UAVs.

Partially Fault Tolerant Centralized Control
Our next objective was to make the previous model fault tolerant. We wanted to give the ability to the swarm to complete the entire mission even if there are losses. Our approach is able to deal only with faulty worker UAVs. We leave the case where the leader UAV is faulty for future work, due to time constraints.

Approach
The first challenge we faced was how the leader could determine which UAVs are still functional and which UAVs have been destroyed, considering that using the ether project delays on messages may occur. The leader makes use of a new map, consisting of all the functional UAVs, and the information they sent in the previous iteration. After the first 6 seconds, the leader checks whether the other UAVs send updated information. This is achieved by comparing the coordinates of the position of a UAV with the coordinates of its position in the previous iteration. A penalty point is given to every UAV that does not update its coordinates in every iteration. The penalty points are returned to zero, if the UAV update its coordinates before it is deleted. A UAV is considered to be non-functional if the UAV does not broadcast updated information for more than a consecutive number of iterations. The leader deletes the faulty UAV from the list of the functional UAVs. Also, if the faulty UAV had been undertaking a task, its task is stored in a map. Afterwards, the leader calculates the list of the remaining functional UAVs. The leader checks whether the list that contains the tasks of the non-functional UAVs is empty of not. If the list is not empty, and one second has passed until the deletion of the faulty UAV, it searches to find the next available UAV that does not undertake any task. When a UAV is available, the leader assigns to that UAV the task which had been assigned to the faulty or destroyed UAV. The lists that hold the non-functional UAVs and their tasks are updated.
The list of UAVs that take part in the mission can change dynamically, so new members can be added into the list during the mission as well.

Design
if exists ri in set elems destroyedUAV & ri = x.#1 then skip
else( if id = leader
 then(
 if card dom uavPositions <> card dom uavPositionsZ and time >= 6E9
   then ( uavPositionsZ := uavPositionsZ ++ uavPositions;
          for all iz in set dom uavPositionsZ do
	      listUAVlag(iz) := 0; -- listUAVlag is the list that keeps
      )				     -- the penalty points
   else ( if uavPositions(x.#1).x = x.#2 and
             uavPositions(x.#1).y = x.#3 and
             uavPositions(x.#1).z = x.#4
	  then (listUAVlag(x.#1) := listUAVlag(x.#1) + 1;)
	  else listUAVlag(x.#1) := 0;
);
if exists kk in set dom listUAVlag & kk = x.#1 and listUAVlag(x.#1) > 10
then ( if exists kki in set dom listOfWorkers & kki = x.#1 
then(--if the UAV had been given a task, then its coordinates are stored				
      mapUAVlag(x.#1) := listOfWorkers(x.#1);
	      listOfWorkers := {x.#1} <-: listOfWorkers;
	      seqUAVlag := seqUAVlag^[x.#1];
	);
-- The destroyed UAV is deleted from the lists of functional UAVs
destroyedUAV := destroyedUAV^[x.#1];
ZERVAKIS_FINAL := time + 1E9;
uavPositions := {x.#1} <-: uavPositions;
uavPositionsZ := {x.#1} <-: uavPositionsZ;
 listUAVlag := {x.#1} <-: listUAVlag;

The leader UAV uses the above part of code to find faulty UAVs. Initially, the leader creates a list of the functional UAVs and sets their penalty values as zeros. In the next iteration, the leader checks whether the received data are equal to the old information. The leader increases the penalty points of a UAV by one, in each iteration that it receives the same data. In the second block, the leader checks both whether the penalty points of a UAV exceeds the value 10 and if the faulty UAV was undertaking a task. If it is true, the UAV is deleted from the list of workers, its task is stored in a map, and the id number of the faulty UAV is stored in the sequence consisting of the faulty UAVs.  In the final block, the leader erases the faulty UAV from the lists consisting of the functional UAVs, and keeps the time that this action took place.

Co-simulation
Simulation results of this project are presented in the next chapter.
