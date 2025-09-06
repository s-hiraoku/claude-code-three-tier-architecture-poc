---
name: advanced-waiting-test
description: Advanced waiting strategies with file-based communication and process management
tools: [Bash, Read]
---

# Advanced Waiting Strategies Test

Testing sophisticated waiting patterns that might overcome the session management issues.

## 🚀 Test 5: File-Based Communication with Heartbeat
Using file system as communication channel:

**Setup communication files**:
!mkdir -p /tmp/claude-comm
!echo "PENDING" > /tmp/claude-comm/status.txt
!echo "$(date): Starting Layer 2 execution" > /tmp/claude-comm/log.txt

**Execute with file-based feedback**:
!claude -p --session-id "filecomm-$(date +%s)" "/command-zundamon" > /tmp/claude-comm/result.txt 2>&1 && echo "COMPLETED" > /tmp/claude-comm/status.txt &

**Heartbeat monitoring**:
!for i in {1..60}; do
    status=$(cat /tmp/claude-comm/status.txt 2>/dev/null || echo "ERROR")
    echo "⏰ $(date '+%H:%M:%S') - Status: $status (Check $i/60)"
    
    if [ "$status" = "COMPLETED" ]; then
        echo "🎉 Layer 2 command completed successfully!"
        echo "=== Result ==="
        cat /tmp/claude-comm/result.txt
        break
    elif [ "$status" = "ERROR" ]; then
        echo "❌ Communication error detected"
        break
    fi
    
    sleep 2
done

---

## 🔄 Test 6: Dual-Session Strategy
Using separate sessions for different commands:

**Session A: Quick test command**:
!echo "Testing with simple command first..."
!timeout 60s claude -p --session-id "session-a-$(date +%s)" "/help" 2>&1 | head -5 || echo "❌ Session A failed"

**Session B: Actual Layer 2 command** (only if Session A succeeds):
!if timeout 60s claude -p --session-id "session-b-$(date +%s)" "/help" >/dev/null 2>&1; then
    echo "✅ Basic Claude CLI working, attempting Layer 2..."
    timeout 180s claude -p --session-id "session-main-$(date +%s)" "/command-zundamon" 2>&1
else
    echo "❌ Basic Claude CLI not working, skipping Layer 2 test"
fi

---

## 🔧 Test 7: Process Tree Analysis
Understanding what happens during execution:

**Before execution - Process baseline**:
!echo "=== Baseline Process Count ==="
!ps aux | grep -i claude | grep -v grep | wc -l | xargs -I {} echo "Claude processes: {}"

**During execution - Live monitoring**:
!claude -p --session-id "monitored-$(date +%s)" "/command-zundamon" > /tmp/live_result.txt 2>&1 &
!LIVE_PID=$!

!echo "=== Live Process Monitoring ==="
!for i in {1..30}; do
    # Check if our process is still running
    if kill -0 $LIVE_PID 2>/dev/null; then
        # Count total Claude processes
        count=$(ps aux | grep -i claude | grep -v grep | wc -l)
        echo "⏱️ ${i}/30 - Our PID: $LIVE_PID, Total Claude processes: $count"
        
        # Check for any output
        if [ -s /tmp/live_result.txt ]; then
            echo "📥 Output detected, waiting for completion..."
        fi
        
        sleep 3
    else
        echo "✅ Process $LIVE_PID completed!"
        break
    fi
done

**After execution - Result check**:
!echo "=== Final Result ==="
!if [ -s /tmp/live_result.txt ]; then
    cat /tmp/live_result.txt
else
    echo "❌ No output captured"
fi

---

## 🧪 Test 8: Resource-Conscious Execution
Checking if resource constraints are the issue:

**Memory check before execution**:
!free -h 2>/dev/null || vm_stat | grep "Pages free" || echo "Memory info not available"

**CPU load check**:
!uptime

**Conservative execution with resource monitoring**:
!echo "🔄 Starting conservative execution..."
!(
    # Run in subshell to limit resource impact
    ulimit -v 1000000 2>/dev/null  # Limit virtual memory if supported
    timeout 90s claude -p --session-id "conservative-$(date +%s)" "/basic-test" 2>&1
) || echo "❌ Conservative execution failed"

---

## 🎯 Test 9: Alternative Command Test
Testing if the issue is specific to our custom commands:

**Test with known working commands**:
!echo "Testing with built-in commands first..."

**Built-in help command**:
!timeout 30s claude -p "/help" 2>&1 | head -3 || echo "❌ Built-in /help failed"

**Test with our basic-test command**:
!timeout 60s claude -p "/basic-test" 2>&1 | head -5 || echo "❌ Our basic-test failed"

**If basic commands work, try character commands**:
!if timeout 30s claude -p "/help" >/dev/null 2>&1; then
    echo "✅ Built-in commands work, trying character commands..."
    timeout 120s claude -p --session-id "char-test-$(date +%s)" "/command-zundamon" 2>&1
else
    echo "❌ Even built-in commands fail, indicating deeper issue"
fi

---

## 📊 Comprehensive Test Results

The tests above will help determine:

1. **File-based communication viability**
2. **Optimal timeout durations**  
3. **Process management patterns**
4. **Resource constraint impacts**
5. **Command-specific vs system-wide issues**

If any waiting strategy succeeds, we'll have discovered a viable path to true 3-tier architecture!