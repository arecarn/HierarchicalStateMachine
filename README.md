# HierarchicalStateMachine
A hierarchical state machine for C or C++


                                  
                     State Oriented Programming
                Hierarchical State Machines in C/C++
                  Miro Samek and Paul Y. Montgomery
                            May 13, 2000

The   code  accompanying  the  ESP  article  is  organized  into  two
subdirectories:  C  and  Cpp.  Each subdirectory  contains  Microsoft
Visual  C++ v 5.0 project to build and run the digital watch  example
discussed  in the article. To compile and run the examples you  would
load  the  workspace (watch.dsw) into the Visual C++ IDE.  The  Debug
versions of both C and C++ projects define preprocess macro HSM_INSTR
and  demonstrate  simple instrumentation of the state  machines.  For
your  convenience we have included the executable files  (watch.exe),
which  you  can try directly. You inject events into the watch  state
machine  by  typing numbers on your keyboard: (0=DATE_EVT, 1=SET_EVT,
2=TICK_EVT).

In  order to use the code in your embedded projects you would need to
extract files hsm.h and hsm.c from the C subdirectory, or files hsm.h
and hsm.cpp form the Cpp subdirectory. We have compiled the code with
a  variety  of  C  and  C++ compilers, including:  VC++  and  Borland
compilers   for  Windows,  g++  compiler  for  Linux,  ARM   Software
Development Toolkit v. 2.0 and 2.5 C compiler, and Green Hills  MULTI
2000  C  and EC++ ARM/THUMB compilers. We have noticed one  potential
problem with one aspect of the C++ implementation. Depending  on  the
compiler  you  would  use  you may encounter  compilation  errors  in
casting  (upcasting) event handlers to a Hsm member function  pointer
(EvtHndlr).  This  upcasting  is necessary  to  configure  the  state
machine  in  the  constructor. In our code we use the  most  commonly
accepted by different compilers cast:

                     (EvtHndlr)&<class>::<func>

newer C++ compilers (but not EC++ compilers) may accept construct:

            reinterpret_cast<EvtHndlr>(&<class>::<func>)

Your compiler may allow you to use a simpler form:

                          (EvtHndlr)<func>

since  specifying  class with the scope operator  ::  should  not  be
necessary inside the class method (the constructor).

Should you have any questions or comments please feel free to contact
us directly:

miro@IntegriNautics.com, or
paulm@IntegriNautics.com

=====================================================================

                             Corrections
                            January 23, 2003

Kevin Flemming at Philips Electronics North America has recently 
alerted us of rather serious bug in the original code. As reported by 
Kevin, our original HSM implementation handles incorrectly some 
inherited transitions, that is transitions originating at the higher 
levels of state hierarchy than the currently active state. More 
specifically, the issue is created by the optimization involving 
one-time only calculation of the least common ancestor (LCA) state, 
which is stored in a static variable shared by all instances of a given
state machine. The bug shows up when the transition is inherited by 
more than one substate. The original code makes no distinction between
the source state for a transition, and the currently active state. In 
case of inherited state transition, the two states are different.


The bug fix designed by Paul Montgomery upholds all the discussion and
claims we made in the article. It is not expensive in terms of either 
memory or CPU cycles, and retains the static LCA optimization. The bug
fix involves augmenting the abstract Hsm base class with one 
additional attribute (State *source). This is a pointer to the 
"source state"--the state from which the transition actually 
originates. (Please note that for the inherited transitions the source
of a transition is *different* from the current state.) Calculation of
the LCA can be still performed statically (done just once) because the
LCA is a function of the state topology only.  This is fixed at 
compile time and is identical for all instances of any Hsm subclass.
Paul's code correctly augments the event processor (Hsm::onEvent() 
method in C++ and HsmOnEvent() in C) to exit the currently active 
state up to the level of the "source state", for which the LCA is 
statically calculated.

We provide a new, fixed state machine code (hsm.h/hsm.cpp for C++ and 
hsm.h/hsm.c for C). Alongside the previous Watch example described in 
the article, we also provide a new test harness for the bug fix (hsmtst
workspace and project in both directories C and Cpp). Please note that
in the meantime we switched from Microsoft Visual C++ v5.0 to v6.0. 

It is also probably worth noting that the bug and the fix pertains only
to the HSM implementation published in the "State Oriented Programming"
ESP article from August 2000, and specifically does *not* pertain to the
implementation of HSM published in the book "Practical Statecharts in 
C/C++" by Miro Samek (CMP Books, 2002). The "Practical Statecharts" 
implementation handles all inherited transitions correctly.
 

We would like to apologize for the bug that escaped our attention and 
testing. At the same time, we'd like to thank Kevin Flemming for finding
and alerting us about the issue.

Sincerely,

Paul 
paulm@IntegriNautics.com

Miro
miro@quantum-leaps.com

