# Simplifying development with the AIandMe Platform

AIandMe has developed a platform for testing LLM-based applications, covering the AI life circle of an app development, both in dev phase (with various levels of testing) and while in production (with a live firewall and async logs investigations - audits).

### How the AIandMe platform works?

The AIandMe platform follows the structure `organisation` → `projects`.

An `organisation` is the top level of management in the platform. Team features (collaboration) is performed on the `organisation` level. Moreover, each organisation has its own subscription level, that is independent of the other organisations a user might also have in the platform.

A `project` is a container for the externally associated GenAI app. Thus, for each GenAI assistant you wish to test and protect via the AIandMe platform, you have to create a single project. Each project consumes quotas from the assigned subscription on the organisation level.