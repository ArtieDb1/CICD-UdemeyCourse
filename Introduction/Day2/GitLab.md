## GitLab architecture
A git lab server holds all of the rewlwvent information about your project. But when you make a pieline, it sends your [project](<../CICD Course/Day1/Resources/.gitlab-ci.yml>) to a gitlab runner to run the pipelines.
You can have many pipelines and runners and te Gitlab Runners run the different projects.
These runners will archive any artifacts caught while executing a job.

## Why Gitlab CI?
1. Modern and up-to-date while Jenkins is losing marketshare
2. Easier to scale
3. Pipeline as a code
4. Merge requests used too