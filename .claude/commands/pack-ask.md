Assist me in doing git commit
** First step: Examine the current branch **
- display the current branch location

** Second step: Confirm branch **
Ask: "Do you confirm to add the new file to the current branch [Branch name}?"

> Wait for my response (Yes/No) before continue

** Third step: If No then **
- Display all branches
- Ask:
  - Type [new] to create new branch
  - Or input one of the existing branches to continue
  > Wait for my response before continue

** Forth step: If Yes then **
- Ask for the name of the new branch
> Wait for my response before continue
- Make the new branch and switch to it (any uncommitted changed will be brought to the new branch)

** Fifth step: If exiting branch is selected **
1. Check if there is any uncommitted changes
2. If there is uncommited change
   - Store the uncommitted change to cache
   - Tell the user: "Changes have been temporarily stored"
3. Switch to the selected branch
4. Attend to restore the saved changes
   ** 4.1 If restore success and no conflict **
   - Tell the user: "Change restored"
   - Continue to step 6
   ** 4.2 If restore failed **
   - Find all the conflicts
   - For all conflict, show them:
     * **Current branch version**: Read from the current branch
     * **Cached version**: Read from stash
     * **Diff**: Show the diff clearly
   - Ask how to handle the conflict
     * Input [1] - Keep the current branch's version (ignore the cached one)
     * Input [2] - Keep the cached version (override the current version)
     * Input [3] - Handle manually by the user (keep stash for later use)
   > Wait for the input to select and continue to step 6

** Sixth step: Add file **
Add the selected file: $ARGUMENTS (if no, then ".")

** Seventh step: Display status **
Show the going to be committed file

** Eighth step: Compose the commit message **
- Based on the changes and compose the message
- Commit message must based on @.claude/commands/commit-rules.md

** Ninth step: Execute **
- Use the composed message to commit

** Tenth step: Display result **
- Show the committed result

Make sure you wait for my response before continue.

