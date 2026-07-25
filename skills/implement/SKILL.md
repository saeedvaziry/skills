---
name: implement
description: Implement the user-asked feature or bugfix.
---

# Implement

## Refinement

- Read the provided context fully to understand the task
- Read the codebase to understand how it works 
- Refine the given task by interviewing the user to not leave any gaps and clarify everything
- Finalize the acceptance criteria

## Plan

- Plan the implementation based on the refinement
- Present the plan to the user. Use plan-presenter skill if available. If not, Use readable markdown.
- Go to implementation if user confirms the plan.
- If user needs changes, Start from Refinement again and this time consider user's feedback on the plan.

## Implementation

- Start editing the code to achieve the acceptance criteria
- Follow the standards of the codebase
- Write tests to cover the implementation but don't overtest
- Use as less code possible to implement
- Avoid writing large files
- Write smaller and re-usable code blocks

## Review

- Spawn a sub-agent to independently review your implementation.
- You are a good developer but you are the worst code reviewer
- Don't pass your opinions to the reviewer sub-agent
- Run the review only one round. Do not loop.
- The reviewer must return PASS or CHANGES NEEDED with what to change.

## Review Fixer

- Spawn a sub-agent to pass the findings of the reviewer to fix them.
- Do not share your opinions to the fixer agent.
- Fixer agent should first verify the findings and then fix the ncessary ones.

## Finalize

Share with user steps to test the implementation visually

## User Feedback

If user shares feedbacks of bugs or issues with the implementation you have few options to continue based on the feedback and your judgment:

- Fix it right away
- Fix and review and review fixer
- Restart the entire flow from top

This is fully depending on your judgment based on the feedback.