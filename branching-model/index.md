# Branching Model

### `master`

* Should always be stable and ready for production environment
* Persistent: always exists
* Develop branch always merges into master (one way)

### `develop`
* All development work gets merged into this branch for testing
* Persistent: always exists
* All testing and QA is done on this branch

### `feature`
(named per feature you are currently working on)
* Should be branched off of develop
* Naming convention: `feature/my-current-feature`
* Finish your work and do your own testing
* When you feel the feature is complete, file a pull request in GitHub. Wait for feedback from project manager.
* If there is feedback, continue making fixes on the same feature branch 
* Once the feature is approved merge it into develop and close the branch.
* Once the branch has been merged and closed on GitHub, be sure to close it on your local system as well. Use a new unique branch name if you need to open work on the same or a similar element again.

### `hotfix`
* Naming convention: `hotfix/my-hotfix`
* Only to be used for quick fixes to urgent bugs reported from production sites
* Branch off of `master`
* Test the fix and commit
* Use `npm run release --` to bump the patch level version and create a tag right in the hotfix branch
* Create a pull request to merge back into master
* Be sure to merge the hotfix into develop and any other active branches too
