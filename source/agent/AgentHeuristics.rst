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

``LCT.IdentifyPreconditionSatisfyingActions``:

    - Identifies when an action is simply satisfying a precondition; if so, it is not "learned", as the precondition is already known.
    - Example: I need a screwdriver to assemble a chair.  [Get a screwdriver.]
    - To be a match, the following must be true:
        - The TMR is current (not pre- or postfix).
        - There must be previous input in the context (this cannot be the first TMR).
        - The main event must be a REQUEST-ACTION with BENEFICIARY = ROBOT.
        - The immediately previous TMR (last input) must have been current (not pre- or postfix).
        - The previous TMR's main event must have a PURPOSE (this is currently the identifier of a precondition utterance).
        - The theme of this TMR's REQUEST-ACTION.THEME must match the THEME of the previous TMR's main event (concept match only).
    - If the above hold, the input is skipped and all other heuristics are disabled.

FR Resolution Heuristics
^^^^^^^^^^^^^^^^^^^^^^^^
These are heuristics for actual process of learning by instruction through language. Maybe not only in language but also by demonstration (when we have adequate vision capabilities).
FR resolution heuristics take a single TMR instance, as well as a list of currently resolved instances from that instance’s TMR (called “resolves”).  The “resolves” list can be modified by the heuristic.
