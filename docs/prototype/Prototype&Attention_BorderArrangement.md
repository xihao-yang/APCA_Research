


# Step x:
```bash
root@ser997947641129:~/workspace/APCA# cd /tmp/chart13_fixed_inspect
root@ser997947641129:/tmp/chart13_fixed_inspect# pwd
/tmp/chart13_fixed_inspect
root@ser997947641129:/tmp/chart13_fixed_inspect# ls -lh APCAChart13LayoutProbe.java
-rw-r--r-- 1 root root 1.6K Jul  8 05:10 APCAChart13LayoutProbe.java
root@ser997947641129:/tmp/chart13_fixed_inspect# ls -lh source/org/jfree/chart/block/BorderArrangement.java

```

# Step X+1
```bash
-rw-r--r-- 1 root root 21K Jul  8 04:20 source/org/jfree/chart/block/BorderArrangement.java
root@ser997947641129:/tmp/chart13_fixed_inspect# cd /tmp/chart13_fixed_inspect
root@ser997947641129:/tmp/chart13_fixed_inspect# BIN="$(defects4j export -p dir.bin.classes)"
Running ant (export.dir.bin.classes)....................................... OK

root@ser997947641129:/tmp/chart13_fixed_inspect# EXTRA_CP="$(defects4j export -p cp.compile || true)"
Running ant (export.cp.compile)............................................ OK

root@ser997947641129:/tmp/chart13_fixed_inspect# if [ -n "$EXTRA_CP" ]; then
  CP="$BIN:$EXTRA_CP"
else
  CP="$BIN"
fi
root@ser997947641129:/tmp/chart13_fixed_inspect# javac -cp "$CP" APCAChart13LayoutProbe.java
root@ser997947641129:/tmp/chart13_fixed_inspect# java -cp "$CP:." APCAChart13LayoutProbe \
> | tee /tmp/chart13_fixed_layout_probe.log
container_width=10.0
container_height=45.6
left_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=12.3,h=45.6]
right_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=10.0,h=45.6]

```

```bash
root@ser997947641129:/tmp/chart13_fixed_inspect# cat /tmp/chart13_fixed_layout_probe.log
container_width=10.0
container_height=45.6
left_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=12.3,h=45.6]
right_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=10.0,h=45.6]
```

# Step X+2

```Bash

root@ser997947641129:/tmp/chart13_fixed_inspect# PATCH_PATH="/root/workspace/paper-reproduction/CACHE/patches/Small/overfitting/FixMiner/Chart/patch1-Chart-13-FixMiner-plausible.patch"
root@ser997947641129:/tmp/chart13_fixed_inspect# echo "$PATCH_PATH"
/root/workspace/paper-reproduction/CACHE/patches/Small/overfitting/FixMiner/Chart/patch1-Chart-13-FixMiner-plausible.patch
root@ser997947641129:/tmp/chart13_fixed_inspect# ls -lh "$PATCH_PATH"
-rw-r--r-- 1 root root 531 Apr 16 15:34 /root/workspace/paper-reproduction/CACHE/patches/Small/overfitting/FixMiner/Chart/patch1-Chart-13-FixMiner-plausible.patch
root@ser997947641129:/tmp/chart13_fixed_inspect# sed -n '1,100p' "$PATCH_PATH"
--- /source/org/jfree/chart/block/BorderArrangement.java
+++ /source/org/jfree/chart/block/BorderArrangement.java
@@ -441,7 +441,7 @@
             h[1] = size.height;
         }
         h[2] = constraint.getHeight() - h[1] - h[0];
-        if (this.leftBlock != null) {
+        if ((this.leftBlock != null) && !(this.rightBlock != null)) {
             RectangleConstraint c3 = new RectangleConstraint(0.0,
                     new Range(0.0, constraint.getWidth()),
                     LengthConstraintType.RANGE, h[2], null,

```

# Step X+3

```bash
root@ser997947641129:/tmp/chart13_fixed_inspect# WORK_PATCHED=/tmp/chart13_fixminer_probe
root@ser997947641129:/tmp/chart13_fixed_inspect# rm -rf "$WORK_PATCHED"
root@ser997947641129:/tmp/chart13_fixed_inspect# defects4j checkout -p Chart -v 13b -w "$WORK_PATCHED"
Checking out 822 to /tmp/chart13_fixminer_probe............................ OK
Init local repository...................................................... OK
Tag post-fix revision...................................................... OK
Excluding broken/flaky tests............................................... OK
Excluding broken/flaky tests............................................... OK
Initialize fixed program version........................................... OK
Apply patch................................................................ OK
Initialize buggy program version........................................... OK
Diff 822:817............................................................... OK
Apply patch................................................................ OK
Tag pre-fix revision....................................................... OK
Check out program version: Chart-13b....................................... OK
root@ser997947641129:/tmp/chart13_fixed_inspect# ls -lah "$WORK_PATCHED"
total 460K
drwxr-xr-x 11 root root 4.0K Jul  8 08:24 .
drwxrwxrwt 36 root root 4.0K Jul  8 08:24 ..
-rw-r--r--  1 root root   61 Jul  8 08:24 .defects4j.config
drwxr-xr-x  8 root root 4.0K Jul  8 08:24 .git
-rw-r--r--  1 root root    5 Jul  8 08:24 .gitignore
drwxr-xr-x  4 root root 4.0K Jul  8 08:23 .svn
-rw-r--r--  1 root root 319K Jul  8 08:24 ChangeLog
-rw-r--r--  1 root root  14K Jul  8 08:24 NEWS
-rw-r--r--  1 root root  33K Jul  8 08:24 README.txt
drwxr-xr-x  2 root root 4.0K Jul  8 08:24 ant
drwxr-xr-x  2 root root 4.0K Jul  8 08:24 checkstyle
-rw-r--r--  1 root root  924 Jul  8 08:24 defects4j.build.properties
drwxr-xr-x  3 root root 4.0K Jul  8 08:24 experimental
drwxr-xr-x  2 root root 4.0K Jul  8 08:24 lib
-rw-r--r--  1 root root  26K Jul  8 08:24 licence-LGPL.txt
-rw-r--r--  1 root root 2.8K Jul  8 08:24 maven-jfreechart-project.xml
drwxr-xr-x  3 root root 4.0K Jul  8 08:24 source
drwxr-xr-x  3 root root 4.0K Jul  8 08:24 swt
drwxr-xr-x  3 root root 4.0K Jul  8 08:24 tests

```

This step is used to get Chart-13b buggy version, not a patched candidate

# Step X+4

```bash

root@ser997947641129:/tmp/chart13_fixminer_probe# find "$WORK_PATCHED" -type f \
>   \( -name "*.java" -o -name "*.xml" -o -name "*.properties" -o -name "*.txt" \) \
>   -exec perl -pi -e 's/\r$//' {} + 2>/dev/null || true
root@ser997947641129:/tmp/chart13_fixminer_probe# cd "$WORK_PATCHED"
root@ser997947641129:/tmp/chart13_fixminer_probe# for p in 1 0 2 3; do
> for p in 1 0 2 3; do
>   if patch -p"$p" --batch < "$PATCH_PATH"; then
>     echo "Applied with -p$p"
>     break
>   fi
> done
> ^C
root@ser997947641129:/tmp/chart13_fixminer_probe# grep -nA6 -B4 "leftBlock != null" \
> source/org/jfree/chart/block/BorderArrangement.java
192-                    RectangleConstraint.NONE);
193-            w[1] = size.width;
194-            h[1] = size.height;
195-        }
196:        if (this.leftBlock != null) {
197-            Size2D size = this.leftBlock.arrange(g2, RectangleConstraint.NONE);
198-            w[2] = size.width;
199-            h[2] = size.height;
200-       }
201-        if (this.rightBlock != null) {
202-            Size2D size = this.rightBlock.arrange(g2, RectangleConstraint.NONE);
--
223-        if (this.bottomBlock != null) {
224-            this.bottomBlock.setBounds(new Rectangle2D.Double(0.0,
225-                    height - h[1], width, h[1]));
226-        }
227:        if (this.leftBlock != null) {
228-            this.leftBlock.setBounds(new Rectangle2D.Double(0.0, h[0], w[2],
229-                    centerHeight));
230-        }
231-        if (this.rightBlock != null) {
232-            this.rightBlock.setBounds(new Rectangle2D.Double(width - w[3],
233-                    h[0], w[3], centerHeight));
--
291-        }
292-        RectangleConstraint c2 = new RectangleConstraint(0.0,
293-                new Range(0.0, width), LengthConstraintType.RANGE,
294-                0.0, null, LengthConstraintType.NONE);
295:        if (this.leftBlock != null) {
296-            Size2D size = this.leftBlock.arrange(g2, c2);
297-            w[2] = size.width;
298-            h[2] = size.height;
299-        }
300-        if (this.rightBlock != null) {
301-            double maxW = Math.max(width - w[2], 0.0);
--
354-            w[1] = size.width;
355-            h[1] = size.height;
356-        }
357-        Range heightRange3 = Range.shift(heightRange, -(h[0] + h[1]));
358:        if (this.leftBlock != null) {
359-            RectangleConstraint c3 = new RectangleConstraint(widthRange,
360-                    heightRange3);
361-            Size2D size = this.leftBlock.arrange(g2, c3);
362-            w[2] = size.width;
363-            h[2] = size.height;
364-        }
--
393-        if (this.bottomBlock != null) {
394-            this.bottomBlock.setBounds(new Rectangle2D.Double(0.0,
395-                    height - h[1], width, h[1]));
396-        }
397:        if (this.leftBlock != null) {
398-            this.leftBlock.setBounds(new Rectangle2D.Double(0.0, h[0], w[2],
399-                    h[2]));
400-        }
401-        if (this.rightBlock != null) {
402-            this.rightBlock.setBounds(new Rectangle2D.Double(width - w[3],
403-                    h[0], w[3], h[3]));
--
440-            Size2D size = this.bottomBlock.arrange(g2, c2);
441-            h[1] = size.height;
442-        }
443-        h[2] = constraint.getHeight() - h[1] - h[0];
444:        if ((this.leftBlock != null) && !(this.rightBlock != null)) {
445-            RectangleConstraint c3 = new RectangleConstraint(0.0,
446-                    new Range(0.0, constraint.getWidth()),
447-                    LengthConstraintType.RANGE, h[2], null,
448-                    LengthConstraintType.FIXED);
449-            Size2D size = this.leftBlock.arrange(g2, c3);
450-            w[2] = size.width;
--
472-        if (this.bottomBlock != null) {
473-            this.bottomBlock.setBounds(new Rectangle2D.Double(0.0, h[0] + h[2],
474-                    w[1], h[1]));
475-        }
476:        if (this.leftBlock != null) {
477-            this.leftBlock.setBounds(new Rectangle2D.Double(0.0, h[0], w[2],
478-                    h[2]));
479-        }
480-        if (this.rightBlock != null) {
481-            this.rightBlock.setBounds(new Rectangle2D.Double(w[2] + w[4], h[0],
482-                    w[3], h[3]));

```

From here, we got Chart-13b + FixMiner patch



# Step X+5

```bash

root@ser997947641129:/tmp/chart13_fixminer_probe# cp /tmp/chart13_fixed_inspect/APCAChart13LayoutProbe.java \
> "$WORK_PATCHED/"
root@ser997947641129:/tmp/chart13_fixminer_probe# diff -u \
> /tmp/chart13_fixed_inspect/APCAChart13LayoutProbe.java \
> "$WORK_PATCHED/APCAChart13LayoutProbe.java"

```

If there is no output, this indicates that the two probes are identical.

This step is very important, as the only variable permitted in the experiment is the programme version.


# Step X+6: Compile patched candidate

```bash

root@ser997947641129:/tmp/chart13_fixminer_probe# cd "$WORK_PATCHED"
root@ser997947641129:/tmp/chart13_fixminer_probe# defects4j compile
Running ant (compile)...................................................... OK
Running ant (compile.tests)................................................ OK
```

After successful, get classpath:
```bash
root@ser997947641129:/tmp/chart13_fixminer_probe# BIN="$(defects4j export -p dir.bin.classes)"
Running ant (export.dir.bin.classes)....................................... OK

root@ser997947641129:/tmp/chart13_fixminer_probe# EXTRA_CP="$(defects4j export -p cp.compile || true)"
Running ant (export.cp.compile)............................................ OK

root@ser997947641129:/tmp/chart13_fixminer_probe# if [ -n "$EXTRA_CP" ]; then
> CP="$BIN:$EXTRA_CP"
> else
> CP="$BIN"
> fi
root@ser997947641129:/tmp/chart13_fixminer_probe# echo "$CP"
build:/tmp/chart13_fixminer_probe/build:/tmp/chart13_fixminer_probe/lib/servlet.jar

```

# Step X+7 

```bash
root@ser997947641129:/tmp/chart13_fixminer_probe# javac -cp "$CP" APCAChart13LayoutProbe.java
root@ser997947641129:/tmp/chart13_fixminer_probe# java -cp "$CP:." APCAChart13LayoutProbe \
> | tee /tmp/chart13_fixminer_layout_probe.log
container_width=10.0
container_height=45.6
left_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=0.0,h=45.6]
right_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=10.0,h=45.6]
```

check:
```bash
root@ser997947641129:/tmp/chart13_fixminer_probe# cat /tmp/chart13_fixminer_layout_probe.log
container_width=10.0
container_height=45.6
left_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=0.0,h=45.6]
right_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=10.0,h=45.6]
```

# Step X+8

```bash

root@ser997947641129:/tmp/chart13_fixminer_probe# diff -u \
> /tmp/chart13_fixed_layout_probe.log \
> /tmp/chart13_fixminer_layout_probe.log
--- /tmp/chart13_fixed_layout_probe.log 2026-07-08 08:13:02.001875573 +0000
+++ /tmp/chart13_fixminer_layout_probe.log      2026-07-08 08:52:35.445725755 +0000
@@ -1,4 +1,4 @@
 container_width=10.0
 container_height=45.6
-left_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=12.3,h=45.6]
+left_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=0.0,h=45.6]
 right_bounds=java.awt.geom.Rectangle2D$Double[x=0.0,y=0.0,w=10.0,h=45.6]

```

# Step X+9

```bash

root@ser997947641129:/tmp/chart13_fixminer_probe# mkdir -p ~/workspace/APCA/cross_project_pilot/behavior_checks/chart13_fixminer_layout
root@ser997947641129:/tmp/chart13_fixminer_probe# cp /tmp/chart13_fixed_layout_probe.log \
> ~/workspace/APCA/cross_project_pilot/behavior_checks/chart13_fixminer_layout/fixed.log
root@ser997947641129:/tmp/chart13_fixminer_probe# cp /tmp/chart13_fixminer_layout_probe.log \
> ~/workspace/APCA/cross_project_pilot/behavior_checks/chart13_fixminer_layout/patched.log

root@ser997947641129:/tmp/chart13_fixminer_probe# diff -u \
> /tmp/chart13_fixed_layout_probe.log \
> /tmp/chart13_fixminer_layout_probe.log \
> > ~/workspace/APCA/cross_project_pilot/behavior_checks/chart13_fixminer_layout/output.diff \
> || true
root@ser99794764

```