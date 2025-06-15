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

diagnostic infos


```
=== About ===
Build version: IntelliJ IDEA 2025.1.2 Build: #IU-251.26094.121 June 3, 2025
Theme: Light
JRE: 21.0.7+9-b895.130, JetBrains s.r.o.
JVM: 21.0.7+9-b895.130, OpenJDK 64-Bit Server VM, JetBrains s.r.o.
Operating System: Linux 6.14.9-200.fc41.x86_64 (amd64)
Toolkit: sun.awt.X11.XToolkit
idea.config.path=/home/user/.config/JetBrains/IntelliJIdea2025.1
idea.system.path=/home/user/.cache/JetBrains/IntelliJIdea2025.1
idea.plugins.path=/home/user/.local/share/JetBrains/IntelliJIdea2025.1
idea.log.path=/home/user/.cache/JetBrains/IntelliJIdea2025.1/log

=== System ===
Number of CPU: 8
Used memory: 2420Mb 
Free memory: 293Mb 
Total memory: 2714Mb 
Maximum available memory: 4096Mb

=== Displays ===
Display 0: 1920x1080; scale: 100%, bounds: 1920x1080 @ (4480; 0), insets: (0; 0; 0; 0)

=== Plugins ===
Custom plugins: [PsiViewer (2025.1), Unit File Support (systemd) (242.250611.343), Tab Shifter (0.36), Rainbow Dash Progress Bar (1.5), Open declaration in opposite group (0.1.0), LSP4IJ (0.13.0), Python Community Edition (251.26094.121), Plugin DevKit (251.26094.98), Bloc (4.1.5), Swing UI Designer (251.26094.98), Claude Code [Beta] (0.1.9-beta), IDE Add-on Development Assistant (Beta) (1.1.1), Indent Rainbow (2.2.0), Python (251.26094.121), Gherkin (251.23774.318), Cucumber for Java (251.23774.318), Grep Console (13.2.0-IJ2023.3), CSV Editor (4.0.2), Minecraft Development (2025.1-1.8.5), LivePlugin (0.9.6 beta), Dart (251.25410.28), Big Data Tools Core (251.26094.121), Big Data File Viewer (251.23774.318), Scala (2025.1.25), GitHub Copilot (1.5.46-243)]
Disabled plugins:[Key Promoter X (2024.2.2), Subversion (251.26094.121), Code With Me (251.26094.121), Perforce Helix Core (251.26094.121), Ansible (1.0.0), Remote File Systems (251.23774.460), Flyway (251.26094.121), Database Navigator (3.5.3.0), Flutter snippet for generator tool (2.232.1), Mercurial (251.26094.121), Lua (1.0.119), Awesome Console (0.1337.12), Clean Architecture (1.0.7), Space (251.23774.318), Ideolog (251.23774.318), Dart Data Class (0.3.3), Code Remark (1.4.0), Flutter Bloc (1.5.0), Flutter Snippets (2.0.0-stable-1), Terraform and HCL (251.23774.426), Flora (beta) (0.5.6), Vineflower (1.2.0), Android (251.26094.121), Gerry Themes (2025.1.0415), Kubernetes (251.26094.121), MidiSwing - Midi Player (0.1.1), .env files (251.23774.318), Flutter (85.3.2)]

=== Project ===
Project trusted: true

=== Garbage Collection ===
Collector G1 Young Generation: count 375, total time 7991 ms
Collector G1 Concurrent GC: count 256, total time 3208 ms
Collector G1 Old Generation: count 15, total time 5225 ms

=== Scala ===



```
