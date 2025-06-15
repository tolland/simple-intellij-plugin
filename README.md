# Simplest possible intellij plugin

I am intermittently seeing this error

```
2025-06-15 14:36:43,043 [   2556] SEVERE - #c.i.u.JreHiDpiUtil - Must be not computed before that call
java.lang.Throwable: Must be not computed before that call
        at com.intellij.ui.JreHiDpiUtil.preload(JreHiDpiUtil.kt:89)
        at com.intellij.ui.scale.JBUIScale.preload(JBUIScale.kt:55)
        at com.intellij.platform.ide.bootstrap.UiKt.initLafAndScale(ui.kt:106)
        at com.intellij.platform.ide.bootstrap.UiKt.access$initLafAndScale(ui.kt:1)

```

when running `runide` gradle task. I spent some time removing and modifying
features in my plugin to try and isolate it. It turns out that its
coming from the platform itself, as this no extension, no code plugin
is reproducing the error intermittently.

```
[simple-plugin-01] $ ./gradlew buildPlugin
Reusing configuration cache.

BUILD SUCCESSFUL in 680ms
13 actionable tasks: 1 executed, 12 up-to-date
Configuration cache entry reused.
[simple-plugin-01] $ ./gradlew runide
Reusing configuration cache.

> Task :runIde
[0.004s][warning][cds] Archived non-system classes are disabled because the java.system.class.loader property is specified (value = "com.intellij.util.lang.PathClassLoader"). To use archived non-system classes, this property must not be set

BUILD SUCCESSFUL in 928ms
10 actionable tasks: 2 executed, 8 up-to-date
Configuration cache entry reused.
[simple-plugin-01] $ ./gradlew runide
Reusing configuration cache.

> Task :runIde
[0.014s][warning][cds] Archived non-system classes are disabled because the java.system.class.loader property is specified (value = "com.intellij.util.lang.PathClassLoader"). To use archived non-system classes, this property must not be set
2025-06-15 14:36:42,191 [   1704]   WARN - #c.i.i.s.p.i.BundledSharedIndexProvider - Bundled shared index is not found at: /home/user/.gradle/caches/8.13/transforms/b51eb5283af486e29033a7f9737be3f1/transformed/ideaIC-2025.1/jdk-shared-indexes
2025-06-15 14:36:43,043 [   2556] SEVERE - #c.i.u.JreHiDpiUtil - Must be not computed before that call
java.lang.Throwable: Must be not computed before that call
        at com.intellij.ui.JreHiDpiUtil.preload(JreHiDpiUtil.kt:89)
        at com.intellij.ui.scale.JBUIScale.preload(JBUIScale.kt:55)
        at com.intellij.platform.ide.bootstrap.UiKt.initLafAndScale(ui.kt:106)
        at com.intellij.platform.ide.bootstrap.UiKt.access$initLafAndScale(ui.kt:1)
        at com.intellij.platform.ide.bootstrap.UiKt$initLafAndScale$1.invokeSuspend(ui.kt)
        at kotlin.coroutines.jvm.internal.BaseContinuationImpl.resumeWith(ContinuationImpl.kt:33)
        at kotlinx.coroutines.DispatchedTask.run(DispatchedTask.kt:104)
        at java.desktop/java.awt.event.InvocationEvent.dispatch(InvocationEvent.java:318)
        at java.desktop/java.awt.EventQueue.dispatchEventImpl(EventQueue.java:781)
        at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:728)
        at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:722)
        at java.base/java.security.AccessController.doPrivileged(AccessController.java:400)
        at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:87)
        at java.desktop/java.awt.EventQueue.dispatchEvent(EventQueue.java:750)
        at com.intellij.ide.IdeEventQueue.dispatchEvent(IdeEventQueue.kt:270)
        at java.desktop/java.awt.EventDispatchThread.pumpOneEventForFilters(EventDispatchThread.java:207)
        at java.desktop/java.awt.EventDispatchThread.pumpEventsForFilter(EventDispatchThread.java:128)
        at java.desktop/java.awt.EventDispatchThread.pumpEventsForHierarchy(EventDispatchThread.java:117)
        at java.desktop/java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:113)
        at java.desktop/java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:105)
        at java.desktop/java.awt.EventDispatchThread.run(EventDispatchThread.java:92)
Caused by: java.lang.Throwable: JreHiDpiUtil is first initialized here
        at com.intellij.ui.JreHiDpiUtil.isJreHiDPIEnabled(JreHiDpiUtil.kt:78)
        at com.intellij.ui.scale.JBUIScale.computeUserScaleFactor(JBUIScale.kt:300)
        at com.intellij.ui.scale.JBUIScale.userScaleFactor$lambda$0(JBUIScale.kt:43)
        at com.intellij.util.concurrency.SynchronizedClearableLazy._get_value_$lambda$1$lambda$0(SynchronizedClearableLazy.kt:41)
        at java.base/java.util.concurrent.atomic.AtomicReference.updateAndGet(AtomicReference.java:210)
        at com.intellij.util.concurrency.SynchronizedClearableLazy.getValue(SynchronizedClearableLazy.kt:40)
        at com.intellij.ui.scale.JBUIScale.scale(JBUIScale.kt:389)
        at com.intellij.util.ui.JBUI.scale(JBUI.java:79)
        at com.intellij.util.ui.JBInsets.<init>(JBInsets.java:48)
        at com.intellij.util.ui.JBInsets.create(JBInsets.java:117)
        at com.intellij.util.ui.UIUtil.getRegularPanelInsets(UIUtil.java:1092)
        at com.intellij.util.ui.UIUtil.<clinit>(UIUtil.java:255)
        at com.intellij.openapi.progress.util.ProgressWindow.<init>(ProgressWindow.java:115)
        at com.intellij.openapi.progress.util.PotemkinProgress.<init>(PotemkinProgress.java:57)
        at com.intellij.openapi.application.impl.ApplicationImpl.lambda$runEdtProgressWriteAction$9(ApplicationImpl.java:978)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWriteAction$lambda$12(AnyThreadWriteThreadingSupport.kt:610)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWriteAction$lambda$15(AnyThreadWriteThreadingSupport.kt:623)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWithTemporaryThreadLocal(AnyThreadWriteThreadingSupport.kt:204)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWriteAction(AnyThreadWriteThreadingSupport.kt:623)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWriteAction(AnyThreadWriteThreadingSupport.kt:610)
        at com.intellij.openapi.application.impl.ApplicationImpl.runEdtProgressWriteAction(ApplicationImpl.java:977)
        at com.intellij.openapi.application.impl.ApplicationImpl.runWriteActionWithNonCancellableProgressInDispatchThread(ApplicationImpl.java:958)
        at com.intellij.openapi.vfs.newvfs.RefreshSessionImpl.fireEvents(RefreshSessionImpl.java:225)
        at com.intellij.openapi.vfs.newvfs.RefreshQueueImpl.fireEvents(RefreshQueueImpl.java:164)
        at com.intellij.openapi.vfs.newvfs.RefreshQueueImpl.lambda$processEvents$6(RefreshQueueImpl.java:144)
        at com.intellij.openapi.application.constraints.ConstrainedTaskExecutor.lambda$submit$1(ConstrainedTaskExecutor.java:41)
        at com.intellij.openapi.application.constraints.ConstrainedTaskExecutor.lambda$submit$4(ConstrainedTaskExecutor.java:64)
        at com.intellij.openapi.application.constraints.BaseConstrainedExecution$Companion.scheduleWithinConstraints$inner(BaseConstrainedExecution.kt:68)
        at com.intellij.openapi.application.constraints.BaseConstrainedExecution$Companion.access$scheduleWithinConstraints$inner(BaseConstrainedExecution.kt:40)
        at com.intellij.openapi.application.constraints.BaseConstrainedExecution$Companion$scheduleWithinConstraints$inner$$inlined$Runnable$1.run(Runnable.kt:20)
        at com.intellij.openapi.application.impl.AppUIExecutorImpl$later$1$schedule$$inlined$Runnable$1.run(Runnable.kt:16)
        at com.intellij.openapi.application.TransactionGuardImpl.runWithWritingAllowed(TransactionGuardImpl.java:240)
        at com.intellij.openapi.application.TransactionGuardImpl.access$100(TransactionGuardImpl.java:25)
        at com.intellij.openapi.application.TransactionGuardImpl$1.run(TransactionGuardImpl.java:202)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runIntendedWriteActionOnCurrentThread$lambda$7(AnyThreadWriteThreadingSupport.kt:319)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWriteIntentReadAction$lambda$6(AnyThreadWriteThreadingSupport.kt:274)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWithTemporaryThreadLocal(AnyThreadWriteThreadingSupport.kt:204)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWriteIntentReadAction(AnyThreadWriteThreadingSupport.kt:274)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runWriteIntentReadAction(AnyThreadWriteThreadingSupport.kt:222)
        at com.intellij.openapi.application.impl.AnyThreadWriteThreadingSupport.runIntendedWriteActionOnCurrentThread(AnyThreadWriteThreadingSupport.kt:318)
        at com.intellij.openapi.application.impl.ApplicationImpl.runIntendedWriteActionOnCurrentThread(ApplicationImpl.java:928)
        at com.intellij.openapi.application.impl.ApplicationImpl$4.run(ApplicationImpl.java:501)
        at com.intellij.util.concurrency.ChildContext$runInChildContext$1.invoke(propagation.kt:102)
        at com.intellij.util.concurrency.ChildContext$runInChildContext$1.invoke(propagation.kt:102)
        at com.intellij.util.concurrency.ChildContext.runInChildContext(propagation.kt:108)
        at com.intellij.util.concurrency.ChildContext.runInChildContext(propagation.kt:102)
        at com.intellij.util.concurrency.ContextRunnable.run(ContextRunnable.java:27)
        at com.intellij.openapi.application.impl.FlushQueue.runNextEvent(FlushQueue.java:117)
        at com.intellij.openapi.application.impl.FlushQueue.flushNow(FlushQueue.java:43)
        ... 14 more
2025-06-15 14:36:43,048 [   2561] SEVERE - #c.i.u.JreHiDpiUtil - IntelliJ IDEA 2025.1  Build #IC-251.23774.435
2025-06-15 14:36:43,048 [   2561] SEVERE - #c.i.u.JreHiDpiUtil - JDK: 21.0.6; VM: OpenJDK 64-Bit Server VM; Vendor: JetBrains s.r.o.
2025-06-15 14:36:43,048 [   2561] SEVERE - #c.i.u.JreHiDpiUtil - OS: Linux
2025-06-15 14:36:43,048 [   2561] SEVERE - #c.i.u.JreHiDpiUtil - Last Action: 
<============-> 92% EXECUTING [3m 6s]
> :runIde
<============-> 92% EXECUTING [3m 7s]

```
