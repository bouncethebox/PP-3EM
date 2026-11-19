# PP-3EM

Power Platform 3-Environment Model for Copilot Studio Agents
Part 01: It’s called the, “3-Environment Model”, but you’ll build four.

Rise of the ten-minute agent
Somebody in HR gets tired of answering the same question about travel reimbursement. They open Copilot Studio, point an agent at the SharePoint site where the policy lives, and ask it a few questions. The answers look good. Ten minutes later, they want to share it with the team.
I’d call that a good use of ten minutes. The part I’d worry about comes six months later, when Finance changes the mileage policy. By then, half the department may be using the agent, the person who built it may have moved on, and the only place anyone knows to try a fix is the copy employees are using.
That’s how you get agent sprawl. Even a dozen agents can give a support team trouble if nobody knows who owns them, which data they use, or where to test a change. If the default environment is where makers land and sharing is the easiest next step, that’s the release process people will use.
Dev, Test, and Prod give them a better one. Power Platform teams have worked this way for years. Agents add a wrinkle or two, especially around publishing, authentication, and who can use the thing once it arrives.

Where the fourth environment fits
If you’re looking at an architecture diagram and counting four environments, you counted correctly. The fourth one is the pipelines host. Makers build in Dev, testers work in Test, and employees use the agent in Prod. The host is where the platform team configures the pipeline and its approval flow. Dataverse holds the run history and the solution packages from those deployments. Treat the host as production infrastructure. If someone retires it during an environment cleanup, they’re also retiring the place you’d look to reconstruct a release.
Before the first maker starts, I’d settle three things with the platform team.
Licensing. Plan for Test and Prod to be Managed Environments. Have the licensing owner check coverage for the people and workloads that will use them. “Everyone already has Microsoft 365” leaves that question open.
Publisher and prefix. Pick the publisher for this program and agree on its prefix. That prefix turns up in component schema names. The first time the team discusses naming should be while somebody can still change the design on a whiteboard.

Pipeline ownership. For this model, I’d make a custom host the tenant default and give makers access to the shared pipeline. That puts the platform team in charge of the production route and replaces personal pipeline creation in the platform host. Give someone responsibility for updating the Pipelines package, too. Then test that they can redeploy a previous version while everyone has time to troubleshoot it.

What a release looks like
The maker starts in Dev and opens the team’s custom solution. If the agent already exists, they can add it and bring in its required components. This is worth checking after someone adds a flow or a tool. An agent that works perfectly in Dev can arrive in Test missing the very thing you wanted the testers to try. Use environment variables for values that change between environments and connection references where the solution needs them.

From the maker’s seat, the first release is straightforward. Update the solution version, open Pipelines in Dev, and deploy to Test. In this setup, that part is self-service. The person responsible for publishing in Test checks authentication and connections, publishes the agent, and hands it to the testers. For the travel agent, I’d ask a tester to sign in with a vanilla user account, ask for the current mileage rate, and open the policy the agent cites.

Once the testers are satisfied, the maker requests promotion of the same package they tested. An approval flow in the host sends the request to the designated approver. If the approver rejects it, the release stops there. 

If they approve it, the configured service principal imports the managed solution into Prod. The maker can request the release without needing production deployment rights. When someone asks which version went out and who approved it, the team has the package, the approval, and the deployment record to refer to.

There’s still a job left after the pipeline reports success. Someone needs to check the production connections, configure user authentication, publish the imported agent, and confirm the intended audience can reach it through the intended channel. Put a name against that job before go-live. Publishing in Dev only publishes the Dev copy.

A few details to check before release day
Try publishing with your actual environment settings. Block unmanaged customizations sounds like an obvious choice for Prod. Microsoft lists a Copilot Studio limitation with that setting enabled: agent publishing doesn’t work. Resolve that with the platform owner while you’re setting up Test. Where you need to leave the setting off, restrict who can customize the target and review unmanaged solution layers for changes.
Choose the audience on the first deployment. If the stage allows sharing requests and agent sharing is available in your region, the maker can request a security group as part of the release. Native deployment sharing applies the first time that object reaches the target. Shipping version two won’t revise the original sharing assignment. Give the group an owner who can manage membership after launch.
Check ownership in the right Entra screen. Stage-owner deployments have a limitation with OAuth connection references, which is one reason to use a service principal for Prod. When configuring it, check ownership under Entra > Enterprise applications. Being listed as an owner under App registrations alone doesn’t satisfy the pipeline requirement.
Give testers the same connector restrictions they’ll face in Prod. If a tool depends on a connector that production policy blocks, a successful test under a looser policy tells you very little. Run the actual tool call under production-equivalent data loss prevention rules. That gives the maker a chance to fix the design before the release is waiting on an approver.
One job I’d automate later is comparing the production agent inventory with the deployment records in the host. Send unmatched agents to the Power Platform admin with the agent name, environment, and any ownership information you can find. An older agent may predate the pipeline or have come through another approved route. Once the admin knows how it got there and who depends on it, they can bring it under management or retire it.

If the 3-Environment Model predates any agents in the environment with no records in Dataverse, kill them, let the Power Platform Admin sort ‘em out.

Why I’d start with Power Platform pipelines
Go back to the person in HR. They know the travel policy well enough to spot a bad answer. They can explain a change to a tester and ask someone to approve it. I can give them that workflow on the Pipelines page they already use in Dev. Asking them to learn a repository and a build process just to correct a reference to the mileage policy is a harder sell.
Azure DevOps makes sense when the team needs it for source control, code review, or a broader build process. It can run alongside Power Platform pipelines. My test for the maker experience is simpler: ask the person who built the agent to explain how they’d get a fix to their coworkers. If they can tell you where they’d test it, who approves it, and who publishes it, the process is probably clear enough to use.
That’s where Part 01 ends, the pipeline can tell you how version 1.3 reached Prod. Now suppose the identity team asks which directory identity belongs to the agent, or the tenant admin wants to find every copy across environments. For a standard-harness Copilot Studio agent with Entra Agent ID integration, the identity exists from the time the maker creates the agent in Dev. It can turn up in inventory well before there’s a production deployment to look up.
Part-02: Microsoft Entra Agent ID picks up with that directory identity. And Part 03: Microsoft Agent 365 covers how administrators find and govern agents across the tenant. Read those alongside the release guide if you’re the person who’ll get the call six months from now, when Finance changes the mileage rate and HR needs to know who can safely fix the answer.
Part-02 and Part-03 coming soon.
