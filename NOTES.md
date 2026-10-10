In my CLAUDE.md, i have commands conventions and architecture. Exact commands on how to run and test the project. I left out the convention about reading .env as it should be in the permissions.

Several permission rules were added

Allow (runs without asking):
- Bash(npm test:*)
- Bash(npm run lint:*)
- Bash(node --test:*)

Ask (prompts you first):
- Bash(git push:*)

Deny (always blocked):
- Read(./.env)
- Edit(./.env)
- Bash(git push --force:*)
- Bash(git push -f:*)

Without the deny rule on the .env file, claude knows my secret keys.
I also denied to push code to the shared repository without my explicit permission.