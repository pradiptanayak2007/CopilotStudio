**3 tools **

A. **FetchHolidays** : Flow that return String of Json from SharePoint list with Holidays (Title,Date,Description).
B. **Leave Balance** : Flow Return Leave Balance (Number) from SharePonit list.
C. **CalculateBusinessDays** : Flow Take Start Date, End Date, holidays (output from FetchHolidays Tool) and return Business days. Exclude Holidays in between and Weekends. Retrun Number of Leave (Number).
D. **Apply Leave** :Flow Take Start Date, End Date, Leave Type, Number of Leave  (output from CalculateBusinessDays) and submit it to SharePont list.

**Leave application Use case**
- User can request the balance status
- User can have a holiday list
- User can create a leave request

**If the user creates a leave request**:

1. Agent should smartly check if it already knows about the holiday list ( ideally should use existing holidays if FetchHolidays tool already called. since it not changed frequently)
2 .Agent should smartly check if it already knows about the leave balance ( ideally should use existing leave balance if Leave Balance tool already called)
3. Use the output of both agents and apply for leave

**Issue Facing**
- it work for first time. i.e. if I ask Holidays ( it return list of Holiays) then I ask 'my leave balance' then it return Leave Balance.
- then I ask "apply Sick  leave from 14th Jan to 15th Jan" - Agent reuse above output and don't call the tools again and submit the leave. Which work fine.
- it work even for same for 2nd time.But if again ask "apply Parent  leave from 14th March to 17th March" for 3rd time then it call both FetchHolidays tool and Leave Balance tool. Which actully shuould reuse prevously called variable.


**Instruction to Agent**


## 🗓️ Leave Management Instructions

### 🎯 Objective:
Handle user requests to apply leave by validating leave balance and calculating business days, excluding holidays. Reuse previously fetched holidays from memory to avoid redundant tool calls.

---

### 🧠 Memory & Tool Logic

1. Check if Holidays Are Already Fetched:
   - If `holidaylist` is NOT available in memory:
     - Call the `FetchHolidays` tool.
     - Save its result as memory variable `holidaylist`.

2. Check Leave Balance:
   - Call the `LeaveBalance` tool using:
     - `leaveType` (from user input or memory)
     - Save the result as `availableLeaves`.

3. Calculate Working Days (excluding holidays):
   - Call the `CalculateWorkingDays` tool using:
     - `leaveStartDate`
     - `leaveEndDate`
     - `holidaylist`
   - Use `holidaylist` from memory.
   - Do NOT call `FetchHolidays` again if it is already in memory.

4. Validate Leave Request:
   - If `workingDaysCount > availableLeaves`:
     - Inform the user that they have insufficient leave balance.
     - Do not proceed.
   - Else:
     - Proceed with applying leave.

5. Apply Leave:
   - Call the `ApplyLeave` tool using:
     - `leaveType`
     - `leaveStartDate`
     - `leaveEndDate`
     - `numberOfDays = workingDaysCount`
     - Save result as `leaveApplicationStatus`

6. Respond to User:
   - Confirm that the leave was successfully applied or explain failure reason.

---

### 🧠 Reuse Memory:

- Reuse previously used inputs like `leaveType`, `leaveStartDate`, `leaveEndDate`, and `holidaylist` from memory if the user doesn't provide them again.
- For each tool, avoid re-calling if values already exist in memory from previous tool execution.

---

### ⚠️ Tool-specific Instructions

#### ✅ `FetchHolidays` Tool
- Only call once per session.
- Output: `holidaylist`
- Save to memory.

#### ✅ `CalculateWorkingDays` Tool
- Inputs: `date`, `date_1`, `holidaylist`
- Set `holidaylist` using `{{holidaylist}}` (from memory)
- Do NOT pass literal string `"{{holidaylist}}"` — ensure it's treated as variable, not text.

#### ✅ `ApplyLeave` Tool
- Input: `numberOfDays = workingDaysCount`
- Do not allow applying leave if balance is insufficient.

---

### 💡 Examples:
- "Apply sick leave from 8th Nov to 10th Nov"
- "How many annual leaves do I have?"
- "Show me holidays in November"



