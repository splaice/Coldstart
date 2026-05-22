# COLDSTART
- Bad name, please ignore.
- This is not a process.
- This is sorta about structure.
- This is not about automation.
- This is closer to a _pattern_ than anything else.
- You need to be engaged.
- You need to read, review and be the editor.
- This pattern has been _good_ for me, it _might_ be a waste of your time.


## WHY?
- Why not.
- Things are moving pretty fast.
- I find myself working differently every week.
- I gave up on heavy handed opinionated plugins and skills for developing code. I found that too often their encoded "right way to make the agent code" was overly heavy handed. I also found they would rot after major model releases. 
- I am currently on the spirit quest to find the least amount of structure needed to have the agent build software in a way that "I prefer".
- I don't have a strategy here. I am just trying things. When something works well I will know it when I see it then try and capture it with as little structure as possible.

## COLDSTART HOWTO
So you have an idea for a software project you want to build but you don't know where to start.

1. Download this repo
2. Open claude/codex in the project
3. Tell the agent to read _BRIEF.md, tell it your idea, tell it to walk you through creating be best BRIEF.md possible. (you deserve the best)
4. Read the BRIEF.md (yes you actually have to read it. You let the agent write it all, now you have to be the editor. Open the file in another terminal. Read it. Talk to the agent about what you just read if you disagree, don't understand, etc)
5. Tell the agent to read _SPEC.md and to create a SPEC.md based on the contents of BRIEF.md
6. Read the SPEC.md (this is where you are going to not want to read it all but you should, you didn't have to write it so you should at the least read it. Again, open it up talk to the agent in the terminal to have it explain things. Since you read it you will see the checklist of things it found it needs addressed. Congrats you read the document!)
7. Ask the agent to walk you through each of the items it found during creation that needs your feedback, attention and decision making (this is where you earn your keep)
8. So now you have a SPEC.md, do a final pass on it. (Yeah right, ask the agent to review the SPEC.md and flag anything that would prevent implementors from building software to the spec)
9. You now have a SPEC.md, you didn't have one before. You didn't write much except for the briefing, now go forth and figure out how to implement it. (Ask the agent).

## WARMSTART HOWTO
So you have an existing code base and you want some structure around directing your agent coders work.

1. Download this repo
2. Copy SPEC.md into your projects root from here
3. Summon your agent and tell it to read the SPEC.md and complete it based on the code that exists in the project.
4. Read the SPEC.md (the hard part... make sure it makes sense, just ask the agent about things that don't make sense or might be wrong)
5. You are done.. You can then use the CHANGE HOWTO to use versions of the SPEC.md to implement new features.

## CHANGES HOWTO
1. Congratulations, you created the software from the SPEC.md.
2. Congratulations, you want to add features or change the software.
3. Create a KILLER_FEATURE_BRIEF.md (Hey claude I have small or MASSIVE feature i want to add, this is what I want to change, describe it in KILLER_FEATURE_BRIEF.md so it complements our existing BRIEF.md)
3. Read it... Agree with it or change it.. (Don't worry you can just ask the agent to make the changes)
4. Update SPEC.md (Hey Agent, review KILLER_FEATURE_BRIEF.md, update SPEC.md to accommodate the new features described in it)
5. READ the diff of SPEC.md, Agree with it or change it, then commit it.
6. Build it (not you, just ask the agent. "Claude, review SPEC.md, create a plan to implement the functionality described in it. Cover the gap between SPEC and the current code base)
7. You have now completed a new feature.

## TODO
- INFRA.md
- HARNESS.md
- PLAN.md

## ACKNOWLEDGEMENTS / REFERENCES
Inspired, copied, stolen from everything below.

- https://github.com/openai/symphony/blob/main/SPEC.md
- https://openai.com/index/open-source-codex-orchestration-symphony/
- https://openai.com/index/harness-engineering/
