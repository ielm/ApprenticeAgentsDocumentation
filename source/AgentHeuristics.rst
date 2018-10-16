Agent Heuristics
================
This document contains brief descriptions and examples of each of the heuristics currently available to the agents (by context).

Current contexts are:

    - “LCT” - Learning Complex Tasks
    - “*” - Generic context (used for standard FR resolution in all contexts)

Pre-Heuristics (constraints on what to learn)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Pre-heuristics accept a full TMR as input, and must return a set of instructions as to whether the agent should continue with learning short term memory and post-heuristics.  Pre-heuristics may also modify the agent’s memory.

``LCT.identify_closing_of_known_task``:

    - Identifies when an utterance is specifying that an already mentioned task or sub-event is now complete.
    - Example: We have assembled a front leg.
    - If the postfix TMR can resolve the main event to the FR, and the resolved event is currently being learned, it is now complete (and all subtasks below it are also completed; any parent task it has is considered current).
    - Finding a match necessarily removes the LEARN_ST_MEMORY and POST_PROCESS agent heuristics as this TMR has been consumed.

``LCT.IdentifyCompletedTaskAgendaProcessor``:

    - Identifies when an utterance signals that the currently learning task (overall) is complete.
    - Example: We finished assembling the chair.
    - This holds true if:
        - The TMR is postfix
        - There is at least one LCT.learned event in working memory
        - There are no LCT.learning events in working memory
    - If this heuristic matches, it will import working memory into long term memory, using the FR import heuristics defined in this context, and then will clear working memory.
