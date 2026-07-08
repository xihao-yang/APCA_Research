# about

Yamasaki -anti LLMs

Koumei -Linux Source Code

Font size -black:bullets
#







# Prototype Setup

## Step3 

```bash
cd ~/workspace/APCA

find cross_project_pilot -maxdepth 3 -type f | sort | head -n 80
```

The above is to confirm:
1. 是否已有 run_chart13_behavior_check.sh；
2. 是否已有 patches；
3. 是否已有 probe Java 文件；
4. 是否已有输出结果；


Chart-13b buggy version + APR overfitting patch

Chart-13f developer fixed version

## Step4 to check whether there is Chart-13 script
```Bash
cd ~/workspace/APCA

find cross_project_pilot -type f | grep -Ei 'chart|behavior|probe|run|patch|diff|summary'

```

This step is used to confirm:
Locate the experiemnt assets such as:
```bash
run_chart13_behavior_check.sh
Chart-13
probe
patch
summary
diff
```

Layer 1: cross-project execution scan

作用：检查多个项目/patch 是否能 checkout、apply、compile、run original tests。

Layer 2: Chart-13 behavior probe

作用：对 Chart-13 的若干 plausible patches 跑 behavior probe，比较 patched candidate version 和 developer fixed version。

## Step5: Open Chart-13 bahavioral probe script
```bash
cd ~/workspace/APCA

sed -n '1,180p' cross_project_pilot/run_chart13_behavior_check.sh
```

```Bash
1. 输入 patch 文件在哪里
2. 输出目录在哪里
3. apply_patch_try 怎么写
4. 是否 checkout Chart-13b
5. 是否 apply APR patch
6. 是否 checkout Chart-13f
7. 是否 compile
8. behavior probe 是怎么注入/运行的
9. correct.log 和 overfitting.log 是如何生成的
10. summary.tsv 是如何生成的
```


## Step6:Meanwhile look the existing sumamry

```bash

cd ~/workspace/APCA

cat cross_project_pilot/behavior_checks/chart13_behavior_summary.txt

echo "---- TSV ----"

cat cross_project_pilot/behavior_checks/chart13_behavior_summary.tsv

```

The step is used to confirm:
1. Chart-13 一共检查了几个 patches
2. 哪些 patch original tests passed
3. 哪些 patch 被 behavior probe 检出差异
4. 哪些没有检出
5. 差异具体是什么


## Step7: Read selected_overfitting_patches.tsv

```bash
cd ~/workspace/APCA

echo "---- file exists? ----"

ls -lh cross_project_pilot/selected_overfitting_patches.tsv

echo "---- header and first rows ----"

head -n 20 cross_project_pilot/selected_overfitting_patches.tsv

echo "---- Chart-13 AVATAR row ----"

grep -n $'Chart-13\tAVATAR' cross_project_pilot/selected_overfitting_patches.tsv
```

The Chart-13 AVATAR patch comes from the CACHE small overfitting patch dataset. I selected it as an executable plausible patch after the cross-project execution scan.


## Step8: ccheck patch file content
```bash
cd ~/workspace/APCA

PATCH_PATH="/root/workspace/paper-reproduction/CACHE/patches/Small/overfitting/AVATAR/Chart/patch1-Chart-13-AVATAR-plausible.patch"

echo "---- patch path ----"

echo "$PATCH_PATH"

echo "---- file info ----"

ls -lh "$PATCH_PATH"

echo "---- first 80 lines ----"

sed -n '1,80p' "$PATCH_PATH"
```

This step is used to confirm:
1. patch 文件真实存在；

2. patch 文件是标准 diff/patch 格式；

3. patch 修改的是 Chart-13 中与 Range constructor 相关的代码。

For meeting:
I inspected the patch file before running the behavior probe. The probe is not arbitrary; it targets the behavior affected by the patch.


### Key finding:


The above content patch is:(The above "-" is deleted in AVATAR patch)：
```java

if (lower > upper) {

-    String msg = "Range(double, double): require lower (" + lower 

-        + ") <= upper (" + upper + ").";

-    throw new IllegalArgumentException(msg);

+            

}

```
based on this, we designed the Rnage (2.0, 1.0) to detect the border deviation, that is also used in Chart-13 detection

##
root@ser997947641129:~/workspace/APCA# head -n 1 cross_project_pilot/behavior_checks/chart13_behavior_summary.tsv
bug     tool    patch   original_tests  behavior_probe  correct_behavior        detected
root@ser997947641129:~/workspace/APCA# grep -n $'Chart-13\tAVATAR' cross_project_pilot/behavior_checks/chart13_behavior_summary.tsv
2:Chart-13      AVATAR  patch1-Chart-13-AVATAR-plausible        PASS    FAIL_no_exception  PASS_THROW       Yes
root@ser997947641129:~/workspace/APCA# find cross_project_pilot/behavior_checks -type f | sort
cross_project_pilot/behavior_checks/chart13_batch/AVATAR_patch1-Chart-13-AVATAR-plausible.run.log
cross_project_pilot/behavior_checks/chart13_batch/Arja-plusible_patch1-Chart-13-Arja-plusible.run.log
cross_project_pilot/behavior_checks/chart13_batch/Cardumen_patch1-Chart-13-Cardumen-plausible.run.log
cross_project_pilot/behavior_checks/chart13_batch/DynaMoth_patch1-Chart-13-DynaMoth-plausible.run.log
cross_project_pilot/behavior_checks/chart13_batch/FixMiner_patch1-Chart-13-FixMiner-plausible.run.log
cross_project_pilot/behavior_checks/chart13_behavior_summary.tsv
cross_project_pilot/behavior_checks/chart13_behavior_summary.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-AVATAR-plausible/apply_level.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-AVATAR-plausible/apply_p1.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-AVATAR-plausible/correct.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-AVATAR-plausible/overfitting.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-AVATAR-plausible/summary.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Arja-plusible/apply_level.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Arja-plusible/apply_p1.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Arja-plusible/correct.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Arja-plusible/overfitting.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Arja-plusible/summary.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Cardumen-plausible/apply_level.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Cardumen-plausible/apply_p1.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Cardumen-plausible/correct.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Cardumen-plausible/overfitting.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-Cardumen-plausible/summary.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-DynaMoth-plausible/apply_level.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-DynaMoth-plausible/apply_p1.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-DynaMoth-plausible/correct.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-DynaMoth-plausible/overfitting.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-DynaMoth-plausible/summary.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-FixMiner-plausible/apply_level.txt
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-FixMiner-plausible/apply_p1.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-FixMiner-plausible/correct.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-FixMiner-plausible/overfitting.log
cross_project_pilot/behavior_checks/chart13_patch1-Chart-13-FixMiner-plausible/summary.txt
cross_project_pilot/behavior_checks/chart13_result_summary.txt
root@ser997947641129:~/workspace/APCA# sed -n '1,220p' cross_project_pilot/run_chart13_behavior_check.sh
#!/usr/bin/env bash
set -euo pipefail

OVER="$(realpath "$1")"
NAME="$(basename "$OVER" .patch)"

WORK="/tmp/apca_chart13_behavior_$NAME"
OUT_DIR="$HOME/workspace/APCA/cross_project_pilot/behavior_checks/chart13_$NAME"

rm -rf "$WORK" "$OUT_DIR"
mkdir -p "$WORK" "$OUT_DIR"

apply_patch_try() {
  local dir="$1"
  local patch_file="$2"

  find "$dir" -type f \( -name "*.java" -o -name "*.xml" -o -name "*.properties" -o -name "*.txt" \) \
    -exec perl -pi -e 's/\r$//' {} + 2>/dev/null || true

  for p in 1 0 2 3; do
    if (cd "$dir" && patch -p"$p" --batch < "$patch_file") > "$OUT_DIR/apply_p${p}.log" 2>&1; then
      echo "[INFO] Applied patch with -p$p"
      echo "-p$p" > "$OUT_DIR/apply_level.txt"
      return 0
    fi
  done

  echo "[ERROR] Failed to apply patch: $patch_file"
  exit 1
}

make_check_java() {
  local dir="$1"

  cat > "$dir/APCAChart13BehaviorCheck.java" <<'EOF'
import org.jfree.data.Range;

public class APCAChart13BehaviorCheck {
    public static void main(String[] args) {
        try {
            Range r = new Range(2.0, 1.0);
            System.out.println("created_range=" + r);
            throw new AssertionError("Expected IllegalArgumentException when lower > upper");
        } catch (IllegalArgumentException expected) {
            System.out.println("PASS_THROW");
        }
    }
}
EOF
}

run_check() {
  local dir="$1"
  local label="$2"

  echo ""
  echo "========== Behavior check: $label =========="

  (
    cd "$dir"

    defects4j compile >/dev/null 2>&1

    make_check_java "$dir"

    BIN="$(defects4j export -p dir.bin.classes)"
    EXTRA_CP="$(defects4j export -p cp.compile || true)"

    if [ -n "$EXTRA_CP" ]; then
      CP="$BIN:$EXTRA_CP"
    else
      CP="$BIN"
    fi

    javac -cp "$CP" APCAChart13BehaviorCheck.java
    java -cp "$CP:." APCAChart13BehaviorCheck
  ) > "$OUT_DIR/${label}.log" 2>&1 || true

  cat "$OUT_DIR/${label}.log"
}

echo "[INFO] Workdir: $WORK"
echo "[INFO] Output dir: $OUT_DIR"
echo "[INFO] Overfitting patch: $OVER"

defects4j checkout -p Chart -v 13b -w "$WORK/overfitting" >/dev/null 2>&1
apply_patch_try "$WORK/overfitting" "$OVER"

defects4j checkout -p Chart -v 13f -w "$WORK/correct" >/dev/null 2>&1

run_check "$WORK/overfitting" "overfitting"
run_check "$WORK/correct" "correct"

cat > "$OUT_DIR/summary.txt" <<EOF
Chart-13 Behavior Check Summary

Patch:
$OVER

Original execution scan:
checkout OK, apply OK, compile OK, original tests pass.

Behavior probe:
Range r = new Range(2.0, 1.0);

Expected behavior:
The constructor should throw IllegalArgumentException when lower > upper.

See:
$OUT_DIR/overfitting.log
$OUT_DIR/correct.log
EOF

echo ""
echo "========== Saved logs =========="
echo "$OUT_DIR/overfitting.log"
echo "$OUT_DIR/correct.log"
echo "$OUT_DIR/summary.txt"
root@ser997947641129:~/workspace/APCA# sed -n '1,120p' /root/workspace/paper-reproduction/CACHE/patches/Small/overfitting/AVATAR/Chart/patch1-Chart-13-AVATAR-plausible.patch
--- /source/org/jfree/data/Range.java
+++ /source/org/jfree/data/Range.java
@@ -82,9 +82,7 @@
      */
     public Range(double lower, double upper) {
         if (lower > upper) {
-            String msg = "Range(double, double): require lower (" + lower 
-                + ") <= upper (" + upper + ").";
-            throw new IllegalArgumentException(msg);
+            
         }
         this.lower = lower;
         this.upper = upper;




## Step 9
```bash
echo "---- Search test-related commands in Chart-13 behavior script ----"

grep -nE 'defects4j test|defects4j compile|test|original_tests|PASS' \

  cross_project_pilot/run_chart13_behavior_check.sh
```

current situation:
45:            System.out.println("PASS_THROW");
62:    defects4j compile >/dev/null 2>&1
101:checkout OK, apply OK, compile OK, original tests pass.

## Step 10
```bash
echo "---- selected_overfitting_patches.tsv header ----"

head -n 1 cross_project_pilot/selected_overfitting_patches.tsv

echo "---- Chart-13 AVATAR selected row ----"

grep -n $'Chart-13\tAVATAR' cross_project_pilot/selected_overfitting_patches.tsv
```

目的：确认 Chart-13 AVATAR 是不是在前序 scan 中被选为 executable plausible patch。

## Step 11
```bash

cd ~/workspace/APCA

echo "---- Who writes chart13_behavior_summary.tsv? ----"
grep -RIn "chart13_behavior_summary.tsv" cross_project_pilot . 2>/dev/null

echo "---- Who writes original_tests field? ----"
grep -RIn "original_tests" cross_project_pilot . 2>/dev/null
```


Purpose:to confirm chart13_behavior_summary.tsv 里的 original_tests PASS 是从哪个脚本或文件写进去的。