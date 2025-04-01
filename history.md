# command list
``` bash
# Push contents
gggg
# Check contents in local
hugo server -D
```
# githujb auth login
```bash
(base) PS C:\workspace\my-proj\hugo-demo> gh auth login
? Where do you use GitHub? GitHub.com
? What is your preferred protocol for Git operations on this host? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Paste an authentication token
Tip: you can generate a Personal Access Token here https://github.com/settings/tokens
The minimum required scopes are 'repo', 'read:org', 'workflow'.
? Paste your authentication token: ****************************************
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as JongYongPark
```

# github repository create
```bash
git init
git add .
git commit -m "🎉 First commit"
gh repo create ryan-portfolio --public --source=. --remote=origin --push

```