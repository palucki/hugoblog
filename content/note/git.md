---
title: "Git tips & tricks"
date: 2021-02-24T23:41:29+01:00
draft: false
---

{{< table_of_contents >}}

## Fetch branch (not current) from remote repo (prepare for rebase):
	git fetch origin release:release

	
## Force push only your current branch:
	git push origin +youBranchName

## Rename local and remote branch 
	# Rename branch locally    
	git branch -m old_branch new_branch
	# Delete the old branch    
	git push origin :old_branch
	# Push the new branch, set local branch to track the new remote
	git push --set-upstream origin new_branch    
	
## Rebase onto 
Providing we have:    

	A--B--C--D--E master
    	
So, what we want to do is tell Git to make
	commit B the newbase of commit D,  	making C go away. This looks like:

	git rebase --onto B D~1

## Automatically push without setting remote branch manually
	git config --global push.default current
	alias push='push -u'
	
## Set mergetool and difftool 
	git config --global merge.tool meld
	it config --global diff.tool meld


## Remove untracked files:
	git clean -n      #  will only show what will be removed with -f
	git clean -f      #  will remove

## Reset one merge result (single file)
	git checkout -m filename.cpp

## Change all commits in repository to new author
	git filter-branch --env-filter '
	WRONG_EMAIL="oldauthor@boss.com"
	NEW_NAME="palucki"
	NEW_EMAIL="piotrek.palucki@gmail.com"

	if [ "$GIT_COMMITTER_EMAIL" = "$WRONG_EMAIL" ]
	then
	    export GIT_COMMITTER_NAME="$NEW_NAME"
	    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
	fi
	if [ "$GIT_AUTHOR_EMAIL" = "$WRONG_EMAIL" ]
	then
	    export GIT_AUTHOR_NAME="$NEW_NAME"
	    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
	fi
	' --tag-name-filter cat -- --branches --tags
