# Bit Platform Development Setup

This README documents the Git workflow and development setup for customizing Bit Platform while maintaining the ability to sync with upstream updates.

## Repository Setup

### Remote Configuration
- **origin**: `https://github.com/strongeye/bitplatform.git` (your fork - for daily development)
- **upstream**: `https://github.com/bitfoundation/bitplatform.git` (original repository - for updates)

### Branch Strategy
- **main**: Clean mirror of upstream/main, used for syncing updates
- **develop**: Your primary development branch where customizations are integrated
- **custom/***: Long-lived branches for major UI customizations
- **feature/***: Short-lived branches for specific features

### Verify Setup
```bash
# Check remotes
git remote -v

# Check branch tracking
git branch -vv

# Should show:
# * develop [origin/develop] 
#   main    [origin/main]
```

## Weekly Upstream Sync Workflow

Run this process weekly to stay current with Bit Platform updates:

### 1. Sync main branch
```bash
# Switch to main
git checkout main

# Fetch latest upstream changes
git fetch upstream

# Merge upstream changes
git merge upstream/main

# Push updated main to your fork
git push origin main
```

### 2. Update develop branch
```bash
# Switch to develop
git checkout develop

# Merge updated main into develop
git merge main

# Resolve any conflicts if they occur
# Push updated develop to your fork
git push origin develop
```

### 3. Update any active feature branches
```bash
# For each active feature branch:
git checkout feature/your-feature
git merge develop
# Resolve conflicts if needed
git push origin feature/your-feature
```

### Quick Sync Script
Save this as `sync-upstream.bat` in your repository root:
```batch
@echo off
echo Syncing with upstream Bit Platform...

git checkout main
git fetch upstream
git merge upstream/main
git push origin main

git checkout develop
git merge main
git push origin develop

echo.
echo Sync complete! Check for conflicts and test your customizations.
pause
```

## Creating Customizations

### 1. Create a Feature Branch
```bash
# Start from develop
git checkout develop

# Create and switch to feature branch
git checkout -b feature/your-feature-name

# Push branch to your fork
git push -u origin feature/your-feature-name
```

### 2. Project Organization for Customizations

Create new projects in the Visual Studio solution following this structure:
```
BitPlatform.sln
├── 01-Upstream/              # Original Bit Platform projects (don't modify)
│   ├── Bit.Core
│   ├── Bit.UI
│   └── Bit.Aspects
├── 02-Extensions/            # Your extensions to Bit Platform
│   ├── YourCompany.Bit.Extensions.Core
│   ├── YourCompany.Bit.Extensions.UI
│   └── YourCompany.Bit.CustomAspects
├── 03-Applications/          # Your custom applications
│   └── YourCustomApp
└── 04-Tests/
    ├── Extensions.Tests
    └── Integration.Tests
```

### 3. Customization Best Practices

**DO:**
- Create new projects for your customizations
- Use Bit Platform's aspect system to extend functionality
- Keep clear separation between upstream and custom code
- Use dependency injection patterns for loose coupling
- Document your customizations

**DON'T:**
- Modify original Bit Platform source files directly
- Mix custom code with upstream code in the same files
- Skip testing after upstream syncs

### 4. Example: Creating a Custom Aspect
```csharp
// YourCompany.Bit.CustomAspects/CustomUIAspect.cs
public class CustomUIAspect
{
    static dependencies = [UIAspect, ThemeAspect];
    
    static async provider([ui, theme]: [UIAspect, ThemeAspect])
    {
        // Register custom components
        ui.registerRoute({
            path: '/custom-dashboard',
            component: CustomDashboard
        });
        
        // Override theme
        theme.registerTheme({
            name: 'custom-brand',
            tokens: customThemeTokens
        });
        
        return new CustomUIAspect();
    }
}
```

## Development Workflow

### Daily Development
1. **Work in feature branches** off `develop`
2. **Commit frequently** with clear messages
3. **Push to origin** (your fork) regularly
4. **Use Visual Studio's Git UI** for basic operations
5. **Use command line** for complex operations like rebasing

### Before Merging Features
```bash
# Update your feature branch with latest develop
git checkout feature/your-feature
git fetch origin
git merge origin/develop

# Test thoroughly
# If tests pass, merge to develop
git checkout develop
git merge feature/your-feature
git push origin develop

# Delete completed feature branch
git branch -d feature/your-feature
git push origin --delete feature/your-feature
```

### Handling Conflicts During Sync
1. **Use Visual Studio's merge tool** for resolving conflicts
2. **Test thoroughly** after resolving conflicts
3. **Document any recurring conflicts** and their solutions
4. **Consider using git rerere** for repeated conflict patterns

## Authentication Setup

### Git Credentials
Stored in: `%USERPROFILE%\.git-credentials`
```
https://strongeye:gho_xxxxxx@github.com
```

### Git Identity
```bash
git config --global user.name "strongeye"
git config --global user.email "your.email@example.com"
```

## Troubleshooting

### Permission Denied Errors
```bash
# Clear cached credentials
del %USERPROFILE%\.git-credentials
# Recreate with correct strongeye credentials

# Or set repository-specific username
git config user.name "strongeye"
git config credential.username "strongeye"
```

### Branch Tracking Issues
```bash
# Fix branch tracking to point to your fork
git checkout main
git push -u origin main

git checkout develop  
git push -u origin develop
```

### Verify Everything is Working
```bash
# Test fetching from both sources
git fetch upstream
git fetch origin

# Check branch status
git status
git branch -vv
```

## Visual Studio Integration

### Opening the Project
1. Open Visual Studio
2. **File > Open > Project/Solution**
3. Navigate to repository folder
4. Open the `.sln` file

### Git Operations in Visual Studio
- **Team Explorer** for repository management
- **Git Repository window** for advanced operations
- **Solution Explorer** shows Git status icons
- **Built-in merge tool** for conflict resolution

### Recommended Extensions
- **GitKraken Glo** for issue tracking
- **Git Diff Margin** for inline diff viewing
- **GitLens** equivalent features built into VS 2022+

## Additional Resources

- [Bit Platform Documentation](https://bit.dev/docs)
- [Bit Platform GitHub](https://github.com/bitfoundation/bitplatform)
- [Git Flow Documentation](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [Visual Studio Git Documentation](https://docs.microsoft.com/en-us/visualstudio/version-control/git-with-visual-studio)

---

*Last updated: $(date)*
*Repository: D:\Trunk\__Kladni_Script*