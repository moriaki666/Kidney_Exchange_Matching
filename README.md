# Kidney_Exchange_Matching
This is a complete implementation of TTCA (Top Trading Cycle Algorithm) made for Kidney Exchange. [ Optimal Matching between Patient and Donor to maximize the expected time till rejection]

# Introduction

This Kidney matching problem is similar to the matching markets problem. The main difference being that there is a waiting list W, which means that not every donor has to be paired up to a patient. Another difference is that the items in the matching markets problem do not care which players acquires said item. However, the donors in the patient-donor pairs care about the outcome of their patients. From now on, time till rejection refers to the time until a patient’s body rejects a certain kidney. This time differs per
patient-donor pair. Donors would only like to participate in the kidney exchange program if it helps their donor pair acquire a higher time till rejection than that patient otherwise would get without participating. 

# Tie-breaking Rules
These tie breaking rules are used for the top trading cycles algorithm. Firstly, we take all of the largest cycles. Then when there are no cycles remaining, we are left with chains. The General chain tie breaking rules are listed below briefly.
-Longest Chains First
-Shortest maximal chain first
-Blood Types

# Implementation
1. First the data is split in to two data frames. The first data frame contains entries correspond to
the expected time until rejection for each combination of patient and donor. The second data frame
contains entries correspond to the blood type for each patient and each donor.
There is a function to get the best donor given a patient which would in turn produce the highest time
till rejection. There is also a function to get the best patient for a given donor. It is checked whether
any patient-donor pair is already a best match. If so they are paired up and removed from the data set.
2. Next all chains are generated, each starting with one donor for all 300 donors. Since in this
implementation all cycles are also chains, we will separate them in the next step into chains and
cycles.
All possible cycles are now generated. Larger cycles have smaller cycles as subsets, so when we
consider the larger cycle, we are accounting for all the subsets. This way all cycles get selected. Since
all cycles eventually get selected we can say that any patient-donor that is part of at-least one cycle
will be accounted for. i.e. the patient will get a kidney with the highest time till rejection and the
corresponding donor will also donate their kidney to the patient who is most compatible.
40 patient-donor pairs get processed through cycles. The rest will be dealt with as chains.
These pairs are added to f_list or finished list which is the list of the patient-donor pairs that
have already been processed i.e all patients in this list have been assigned their most preferred
kidney and the donors involved with their respective patient have donated their kidney to the
best possible patient. The remaining patient-donor pairs are part of new_list which will contain
the list of unmatched or unassigned patient-donor pairs for the remainder of this implementation.
3. After this the function mod_chains() generates all possible chains containing the patient-donor pairs
that have not been dealt with via cycles and it takes new_list as an input i.e. chains for pairs that
are free. There are some chains that have smaller chains as a subset. This function takes around
1 minute to run for the first time since it has 300 􀀀 40 = 260 pairs to process but gets faster after
every successive iteration. In this case when dealing with chains we have to re-calculate all the
available chains since after we process each chain not all members in the chain are accounted for and
this function has to be called every time new_list gets updated. This function also sorts the chains
according the descending order of their length which makes implementing the first_tie breaking rule
of longest chain first 2.1 easier later on. Any chain should at the very least contain 3 members
otherwise we cannot classify that as a chain. .
4. When we process a chain, every patient-donor pair except for the first and last member of the chain
is completed. i.e. the patient in that pair has received a kidney and the corresponding donor has
donated their kidney to another patient. For the first member of a chain, we have an unassigned
patient and for the last member of the chain we have an unassigned donor. The unassigned patient
gets added to the waiting list denoted in the implementation as unassigned_patient and the
unassigned donor gets added to the free_donor list. We will process these two lists after we have
dealt with all possible chains.
5. Now we process the chains. i.e add the first member to unassigned list, the last member to the
free donor list and add the remaining members of the chain to the list of pairs already processed.
This allows us to create new chains with only the pairs that have not been assigned a kidney
and does not have a free donor. The function process_chain() takes does this job of processing a chain.
6. The function calculate_max_chain is used to calculate the maximal distance of a chain which will
allow us to calculate the next tie-breaking mechanism if there is no one longest chain.
7. The function chain_selector() is responsible for selecting a chain given the new_list as input. This
function is responsible for carrying out the tie-breaking rules for selecting a chain namely :
(a) first it looks for the longest chain and there is only chain longest chain then it returns that
chain
(b) if there are multiple longest chains of the same length then it calculates the time till rejection
for all the longest chains and selects the one with the highest total expected time till rejection.
(c) if both the longest chains have the same total expected time till rejection then selects the chain
which starts with a donor of blood type O.
In each iteration we call the chain_selector() function to select a chain and then process it. After
each iteration the list of possible chains decreases as new pairs get added to the finished list and
pairs get removed from the new_list.
Once we have processed all possible chains we are left with 124 completed pairs i.e. finished list,
128 unassigned pairs i.e new_list, 24 patients in the waiting list and 24 free donors who will donate
their kidneys to the waiting list.
8. Then we check the list of patients who have their best donors in the unassigned list of pairs. The
patients in this list receive their most preferred kidney and their corresponding donors are added to
the free donor list where they can donate their kidney to the waiting list. The patients of the donors
who donate their kidneys to the patients in this list are added to the waiting list of unassigned patients.
9. Then we check the list of donors who have their most preferred or best patient in the unassigned
list of pairs. The donors in this list have donated their kidneys to their best patient and their
corresponding patients will go on the waiting list or list of unassigned patients and the donors of
these best patients will in turn donate their kidneys to the waiting list.
10. Now we finally process the waiting list and the list of free donors. If any of the patients in the
unassigned_patient list or waiting list have their preferred donor in the free_donor list they
get matched. In that case the patients get taken of the waiting list and the donor gets taken off
the free_donor list and both the patient and the donor get assigned to the finished list as these
respective patient-donor pairs have been matched.
11. Lastly we check if any of the patients in the unassigned pairs list i.e new_list have their most
preferred donor in the free_donor list. Then we remove these donors from the free_donor list
and add them to the finished list as these pairs now have both their patients and donors matched.
As for the patients in the unassigned pairs list i.e new_list who just got a kidney, their donors will
donate their kidney to the waiting list i.e are added to the free_donor list.

# Results
For all pairs in the finished list, every patient has received their most preferred kidney and every donor
has donated their kidney to best possible patient. Also every patient of the corresponding donor in the
free_donort has been assigned their most preferred kidney so we calculate the total expected time till
rejection for these pairs. Here in this implementation we have 149 + 48 = 197 patients who received
a kidney and 55 patient-donor pairs which remain unmatched and 48 patients have not been assigned a
kidney.
The total expected time till rejection comes out to be 3341.41 years for 197 patients which brings the
average expected time till rejection for the assigned patients to 16.96
