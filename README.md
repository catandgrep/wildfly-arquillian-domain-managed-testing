# Problem

A simple test running with _wildfly-arquillian-container-domain-managed_ results in deployment error:

```org.jboss.arquillian.container.spi.client.container.DeploymentException: Could not deploy to container```

with 'root'-exception:

```Caused by: org.jboss.as.controller.client.helpers.domain.UpdateFailedException: "WFLYDC0022: Operation failed or was rolled back on all servers."```

_See full stack trace at the end of this file_


The more interesting Log is from the Host Controller, _see below_.


# Reproduce

* Clone this project
* run ```mvn clean verify```


### Full trace from Host Controller when switching Log Level DEBUG: 

<pre>
2015-10-07 22:38:17,170 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0025: WildFly Full 9.0.1.Final (WildFly Core 1.0.1.Final) (Host Controller) started in 28437ms - Started 50 of 52 services (15 services are lazy, passive or on-demand)
2015-10-07 22:38:30,825 INFO  [org.jboss.as.repository] (management-handler-thread - 3) WFLYDR0001: Content added at location /home/aweise/dev_server/wildfly-9.0.1.Final/domain/data/content/28/37e96f4e182883773d7bc18f47a84d5a4314b6/content
2015-10-07 22:38:30,981 DEBUG [org.jboss.as.host.controller] (Host Controller Service Threads - 33) failed to process task {
    "operation" => "composite",
    "steps" => [
        {
            "operation" => "add",
            "address" => [("deployment" => "test.war")],
            "operation-headers" => {},
            "runtime-name" => "test.war",
            "content" => [{"hash" => bytes {
                0x28, 0x37, 0xe9, 0x6f, 0x4e, 0x18, 0x28, 0x83,
                0x77, 0x3d, 0x7b, 0xc1, 0x8f, 0x47, 0xa8, 0x4d,
                0x5a, 0x43, 0x14, 0xb6
            }}]
        },
        {
            "operation" => "deploy",
            "address" => [("deployment" => "test.war")],
            "operation-headers" => {}
        }
    ],
    "address" => [
        ("host" => "master"),
        ("server" => "server-one")
    ]
}: java.lang.AssertionError
	at org.jboss.as.controller.OperationContextImpl.readResourceFromRoot(OperationContextImpl.java:707)
	at org.jboss.as.controller.transform.ResourceTransformationContextImpl.create(ResourceTransformationContextImpl.java:60)
	at org.jboss.as.controller.transform.TransformersImpl.transformOperation(TransformersImpl.java:88)
	at org.jboss.as.controller.TransformingProxyController$Factory$1.transformOperation(TransformingProxyController.java:101)
	at org.jboss.as.controller.TransformingProxyController$TransformingProxyControllerImpl.transformOperation(TransformingProxyController.java:153)
	at org.jboss.as.domain.controller.operations.coordination.MultiphaseOverallContext.transformServerOperation(MultiphaseOverallContext.java:176)
	at org.jboss.as.domain.controller.operations.coordination.DomainRolloutStepHandler$2.execute(DomainRolloutStepHandler.java:315)
	at org.jboss.as.domain.controller.plan.ServerTaskExecutor.executeTask(ServerTaskExecutor.java:83)
	at org.jboss.as.domain.controller.plan.ConcurrentServerGroupUpdateTask.execute(ConcurrentServerGroupUpdateTask.java:53)
	at org.jboss.as.domain.controller.plan.AbstractServerGroupRolloutTask$1.run(AbstractServerGroupRolloutTask.java:63)
	at org.jboss.as.domain.controller.plan.AbstractServerGroupRolloutTask$1.run(AbstractServerGroupRolloutTask.java:59)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:356)
	at org.jboss.as.controller.AccessAuditContext.doAs(AccessAuditContext.java:81)
	at org.jboss.as.domain.controller.plan.AbstractServerGroupRolloutTask.run(AbstractServerGroupRolloutTask.java:59)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
	at java.util.concurrent.FutureTask.run(FutureTask.java:262)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:745)
	at org.jboss.threads.JBossThread.run(JBossThread.java:320)

2015-10-07 22:38:31,110 DEBUG [org.jboss.as.controller.management-operation] (management-handler-thread - 2) WFLYCTL0017: Operation ("undeploy") failed - address: ([
    ("server-group" => "main-server-group"),
    ("deployment" => "test.war")
]) - failure description: "WFLYCTL0216: Management resource '[
    (\"server-group\" => \"main-server-group\"),
    (\"deployment\" => \"test.war\")
]' not found"
2015-10-07 22:38:31,232 DEBUG [org.jboss.as.controller.management-operation] (management-handler-thread - 1) Entered VERIFY stage; waiting for service container to settle
2015-10-07 22:38:31,390 DEBUG [org.jboss.as.domain.management] (MSC service thread 1-8) Stopping 'ApplicationRealm' Security Realm Service
2015-10-07 22:38:31,399 INFO  [org.jboss.as.host.controller] (Host Controller Service Threads - 33) WFLYHC0024: Stopping server server-two
2015-10-07 22:38:31,424 DEBUG [org.jboss.as.domain.management] (MSC service thread 1-5) Stopping 'ManagementRealm' Security Realm Service
2015-10-07 22:38:31,427 ERROR [stderr] (Host Controller Service Threads - 33) Exception in thread "Host Controller Service Threads - 33" java.lang.AssertionError
2015-10-07 22:38:31,428 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.protocol.mgmt.ActiveOperationSupport.registerActiveOperation(ActiveOperationSupport.java:151)
2015-10-07 22:38:31,428 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.protocol.mgmt.ActiveOperationSupport.registerActiveOperation(ActiveOperationSupport.java:121)
2015-10-07 22:38:31,428 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.protocol.mgmt.ManagementChannelHandler.initializeOperation(ManagementChannelHandler.java:115)
2015-10-07 22:38:31,429 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.controller.remote.TransactionalProtocolClientImpl.execute(TransactionalProtocolClientImpl.java:119)
2015-10-07 22:38:31,429 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ManagedServerProxy.execute(ManagedServerProxy.java:98)
2015-10-07 22:38:31,429 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ManagedServerProxy.execute(ManagedServerProxy.java:83)
2015-10-07 22:38:31,429 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.controller.remote.TransactionalProtocolHandlers.executeBlocking(TransactionalProtocolHandlers.java:75)
2015-10-07 22:38:31,429 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ManagedServer$ServerStopTask.execute(ManagedServer.java:869)
2015-10-07 22:38:31,429 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ManagedServer.internalSetState(ManagedServer.java:555)
2015-10-07 22:38:31,430 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ManagedServer.stop(ManagedServer.java:258)
2015-10-07 22:38:31,430 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ServerInventoryImpl.stopServers(ServerInventoryImpl.java:339)
2015-10-07 22:38:31,430 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ServerInventoryImpl.shutdown(ServerInventoryImpl.java:437)
2015-10-07 22:38:31,430 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.as.host.controller.ServerInventoryService$1.run(ServerInventoryService.java:128)
2015-10-07 22:38:31,430 ERROR [stderr] (Host Controller Service Threads - 33) 	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
2015-10-07 22:38:31,430 ERROR [stderr] (Host Controller Service Threads - 33) 	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
2015-10-07 22:38:31,431 ERROR [stderr] (Host Controller Service Threads - 33) 	at java.lang.Thread.run(Thread.java:745)
2015-10-07 22:38:31,443 INFO  [org.jboss.as.host.controller] (MSC service thread 1-6) WFLYHC0027: Unregistering server server-one
2015-10-07 22:38:31,449 ERROR [stderr] (Host Controller Service Threads - 33) 	at org.jboss.threads.JBossThread.run(JBossThread.java:320)
2015-10-07 22:38:31,455 INFO  [org.jboss.as.host.controller] (MSC service thread 1-6) WFLYHC0027: Unregistering server server-two
2015-10-07 22:38:31,516 DEBUG [org.jboss.as.host.controller] (Read thread for /127.0.0.1:33487) process controller connection closed.
2015-10-07 22:38:31,545 INFO  [org.jboss.as] (MSC service thread 1-1) WFLYSRV0050: WildFly Full 9.0.1.Final (WildFly Core 1.0.1.Final) stopped in 153ms
</pre>

### Full stack trace from the Test itself:

<pre>
org.test.DomainTest  Time elapsed: 19.146 sec  <<< ERROR!
org.jboss.arquillian.container.spi.client.container.DeploymentException: Could not deploy to container
	at org.jboss.as.arquillian.container.domain.ArchiveDeployer.deploy(ArchiveDeployer.java:73)
	at org.jboss.as.arquillian.container.domain.ServerGroupContainer.deploy(ServerGroupContainer.java:103)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$3.call(ContainerDeployController.java:161)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$3.call(ContainerDeployController.java:128)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.executeOperation(ContainerDeployController.java:271)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.deploy(ContainerDeployController.java:127)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.invokeObservers(EventContextImpl.java:99)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:81)
	at org.jboss.arquillian.container.impl.client.ContainerDeploymentContextHandler.createDeploymentContext(ContainerDeploymentContextHandler.java:78)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.container.impl.client.ContainerDeploymentContextHandler.createContainerContext(ContainerDeploymentContextHandler.java:57)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.container.impl.client.container.DeploymentExceptionHandler.verifyExpectedExceptionDuringDeploy(DeploymentExceptionHandler.java:50)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:145)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:116)
	at org.jboss.arquillian.core.impl.EventImpl.fire(EventImpl.java:67)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$1.perform(ContainerDeployController.java:95)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$1.perform(ContainerDeployController.java:80)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.forEachDeployment(ContainerDeployController.java:263)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.forEachManagedDeployment(ContainerDeployController.java:239)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.deployManaged(ContainerDeployController.java:79)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.invokeObservers(EventContextImpl.java:99)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:81)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:145)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:116)
	at org.jboss.arquillian.core.impl.EventImpl.fire(EventImpl.java:67)
	at org.jboss.arquillian.container.test.impl.client.ContainerEventController.execute(ContainerEventController.java:101)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.invokeObservers(EventContextImpl.java:99)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:81)
	at org.jboss.arquillian.test.impl.TestContextHandler.createClassContext(TestContextHandler.java:92)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.test.impl.TestContextHandler.createSuiteContext(TestContextHandler.java:73)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:145)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:116)
	at org.jboss.arquillian.test.impl.EventTestRunnerAdaptor.beforeClass(EventTestRunnerAdaptor.java:87)
	at org.jboss.arquillian.junit.Arquillian$2.evaluate(Arquillian.java:201)
	at org.jboss.arquillian.junit.Arquillian.multiExecute(Arquillian.java:422)
	at org.jboss.arquillian.junit.Arquillian.access$200(Arquillian.java:54)
	at org.jboss.arquillian.junit.Arquillian$3.evaluate(Arquillian.java:218)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.jboss.arquillian.junit.Arquillian.run(Arquillian.java:166)
	at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:283)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeWithRerun(JUnit4Provider.java:173)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:153)
	at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:128)
	at org.apache.maven.surefire.booter.ForkedBooter.invokeProviderInSameClassLoader(ForkedBooter.java:203)
	at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:155)
	at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:103)
Caused by: org.jboss.as.controller.client.helpers.domain.UpdateFailedException: "WFLYDC0022: Operation failed or was rolled back on all servers."
	at org.jboss.as.controller.client.helpers.domain.impl.DomainDeploymentPlanResultFuture.createFailureResults(DomainDeploymentPlanResultFuture.java:133)
	at org.jboss.as.controller.client.helpers.domain.impl.DomainDeploymentPlanResultFuture.getResultFromNode(DomainDeploymentPlanResultFuture.java:105)
	at org.jboss.as.controller.client.helpers.domain.impl.DomainDeploymentPlanResultFuture.get(DomainDeploymentPlanResultFuture.java:87)
	at org.jboss.as.controller.client.helpers.domain.impl.DomainDeploymentPlanResultFuture.get(DomainDeploymentPlanResultFuture.java:49)
	at org.jboss.as.arquillian.container.domain.ArchiveDeployer.executeDeploymentPlan(ArchiveDeployer.java:90)
	at org.jboss.as.arquillian.container.domain.ArchiveDeployer.deploy(ArchiveDeployer.java:62)
	at org.jboss.as.arquillian.container.domain.ServerGroupContainer.deploy(ServerGroupContainer.java:103)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$3.call(ContainerDeployController.java:161)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$3.call(ContainerDeployController.java:128)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.executeOperation(ContainerDeployController.java:271)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.deploy(ContainerDeployController.java:127)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.invokeObservers(EventContextImpl.java:99)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:81)
	at org.jboss.arquillian.container.impl.client.ContainerDeploymentContextHandler.createDeploymentContext(ContainerDeploymentContextHandler.java:78)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.container.impl.client.ContainerDeploymentContextHandler.createContainerContext(ContainerDeploymentContextHandler.java:57)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.container.impl.client.container.DeploymentExceptionHandler.verifyExpectedExceptionDuringDeploy(DeploymentExceptionHandler.java:50)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:145)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:116)
	at org.jboss.arquillian.core.impl.EventImpl.fire(EventImpl.java:67)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$1.perform(ContainerDeployController.java:95)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController$1.perform(ContainerDeployController.java:80)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.forEachDeployment(ContainerDeployController.java:263)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.forEachManagedDeployment(ContainerDeployController.java:239)
	at org.jboss.arquillian.container.impl.client.container.ContainerDeployController.deployManaged(ContainerDeployController.java:79)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.invokeObservers(EventContextImpl.java:99)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:81)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:145)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:116)
	at org.jboss.arquillian.core.impl.EventImpl.fire(EventImpl.java:67)
	at org.jboss.arquillian.container.test.impl.client.ContainerEventController.execute(ContainerEventController.java:101)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.invokeObservers(EventContextImpl.java:99)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:81)
	at org.jboss.arquillian.test.impl.TestContextHandler.createClassContext(TestContextHandler.java:92)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.test.impl.TestContextHandler.createSuiteContext(TestContextHandler.java:73)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at org.jboss.arquillian.core.impl.ObserverImpl.invoke(ObserverImpl.java:94)
	at org.jboss.arquillian.core.impl.EventContextImpl.proceed(EventContextImpl.java:88)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:145)
	at org.jboss.arquillian.core.impl.ManagerImpl.fire(ManagerImpl.java:116)
	at org.jboss.arquillian.test.impl.EventTestRunnerAdaptor.beforeClass(EventTestRunnerAdaptor.java:87)
	at org.jboss.arquillian.junit.Arquillian$2.evaluate(Arquillian.java:201)
	at org.jboss.arquillian.junit.Arquillian.multiExecute(Arquillian.java:422)
	at org.jboss.arquillian.junit.Arquillian.access$200(Arquillian.java:54)
	at org.jboss.arquillian.junit.Arquillian$3.evaluate(Arquillian.java:218)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.jboss.arquillian.junit.Arquillian.run(Arquillian.java:166)
	at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:283)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeWithRerun(JUnit4Provider.java:173)
	at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:153)
	at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:128)
	at org.apache.maven.surefire.booter.ForkedBooter.invokeProviderInSameClassLoader(ForkedBooter.java:203)
	at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:155)
	at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:103)
</pre>