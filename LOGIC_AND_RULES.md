# OddFox Eligibility Checker - Logic and Rules Documentation

**Version:** 2.10.3
**Last Updated:** 2026-01-02

This document explains all the rules, logic, and algorithms used in the OddFox Eligibility Checker for JJIF competitions.

---

## Table of Contents

1. [Age Categories](#age-categories)
2. [Weight Classes](#weight-classes)
3. [Disciplines](#disciplines)
4. [Age Move-Up Rules](#age-move-up-rules)
5. [Weight Move-Up Logic](#weight-move-up-logic)
6. [Belt Progressions (Newaza Only)](#belt-progressions-newaza-only)
7. [Smart Merge Optimizer Algorithm](#smart-merge-optimizer-algorithm)
8. [Merge Type Priority](#merge-type-priority)
9. [Plus Weight Handling](#plus-weight-handling)
10. [CSV Analysis Logic](#csv-analysis-logic)

---

## Age Categories

Age categories are determined by the age reached in the competition year (eventYear - birthYear).

### Standard Age Categories

| Category | Age Range | Label | Notes |
|----------|-----------|-------|-------|
| **BABY_MONKEYS** | 3-4 years | Baby Monkeys | Sumo only, no move-ups |
| **U8** | 6-7 years | Youth | Under 8 |
| **U10** | 8-9 years | Youth | Under 10 |
| **U12** | 10-11 years | Youth | Under 12 |
| **U14** | 12-13 years | Youth | Under 14 |
| **U16** | 14-15 years | Youth | Under 16 |
| **U18** | 16-17 years | Youth | Under 18 |
| **U21** | 18-20 years | Adult | Under 21 |
| **ADULT** | 18-99 years | Adult | Open age |
| **MASTERS** | 35-99 years | Masters | 35+ only |

**Important:** Masters (35+) takes priority over Adult when age ≥ 35.

---

## Weight Classes

Weight classes are defined per gender and age category. All weights are in kilograms (kg).

### Male Weight Classes

| Age Category | Weight Classes (kg) |
|--------------|---------------------|
| BABY_MONKEYS | 20 |
| U8 | 20, 22, 25, 28, 32, 36, 40 |
| U10 | 22, 25, 28, 32, 36, 40, 44 |
| U12 | 25, 28, 32, 36, 40, 44, 48, 52 |
| U14 | 32, 36, 40, 44, 48, 52, 56, 62, 69 |
| U16 | 40, 44, 48, 52, 56, 62, 69, 77 |
| U18 | 48, 52, 56, 62, 69, 77, 85 |
| U21 | 56, 62, 69, 77, 85, 94 |
| ADULT | 56, 62, 69, 77, 85, 94 |
| MASTERS | 56, 62, 69, 77, 85, 94 |

### Female Weight Classes

| Age Category | Weight Classes (kg) |
|--------------|---------------------|
| BABY_MONKEYS | 20 |
| U8 | 18, 20, 22, 25, 28, 32, 36 |
| U10 | 20, 22, 25, 28, 32, 36, 40 |
| U12 | 22, 25, 28, 32, 36, 40, 44, 48 |
| U14 | 25, 28, 32, 36, 40, 44, 48, 52, 57 |
| U16 | 32, 36, 40, 44, 48, 52, 57, 63 |
| U18 | 40, 44, 48, 52, 57, 63, 70 |
| U21 | 45, 48, 52, 57, 63, 70 |
| ADULT | 45, 48, 52, 57, 63, 70 |
| MASTERS | 45, 48, 52, 57, 63, 70 |

### Weight Classification Logic

- **Minus Weight (-X kg):** Athlete weighs ≤ X kg
- **Plus Weight (+X kg):** Athlete weighs > X kg (where X is the maximum weight in the category)
- Athletes are assigned to their weight class based on their registered weight
- Weight move-up means moving to the next heavier weight class

---

## Disciplines

The system supports four disciplines with different rules:

### 1. Ju-Jitsu Fighting (FIGHTING)
- Full contact Ju-Jitsu fighting
- Age categories: U8, U10, U12, U14, U16, U18, U21, ADULT, MASTERS
- No belts
- Age move-ups allowed (see Age Move-Up Rules)

### 2. Jiu-Jitsu Newaza / BJJ (NEWAZA)
- Ground fighting only
- Age categories: U8, U10, U12, U14, U16, U18, U21, ADULT, MASTERS
- **Belt system applies** (see Belt Progressions)
- Age move-ups allowed (see Age Move-Up Rules)

### 3. Groundfighting (GROUNDFIGHT)
- Specific groundfighting discipline
- Age categories: U10, U12 only
- No belts
- Limited age move-up: U10 → U12 only

### 4. Baby Monkeys Sumo (BABY_MONKEYS)
- Age: 3-4 years only
- Gender labels: "Boys" (MALE) / "Girls" (FEMALE)
- Weights: -20 kg and (+)20 kg only
- **No age move-ups allowed**
- **No belts**

---

## Age Move-Up Rules

Age move-ups allow athletes to compete in a higher age category. Each discipline has specific rules:

### Ju-Jitsu Fighting (FIGHTING)

| From Category | Can Move Up To | Reason |
|---------------|----------------|---------|
| U8 | U10 | Allowed |
| U10 | U12 | Allowed |
| U12 | U14 | Allowed |
| U18 | U21 | Allowed |
| U21 | ADULT | Allowed |
| Others | ❌ No move-up | Different fight durations or technique restrictions |

**Restrictions:**
- U14 → U16: Not allowed (different fight durations)
- U16 → U18: Not allowed (U18 allows dangerous techniques)
- ADULT → MASTERS: Not allowed (but Masters can compete in Adult)

### Jiu-Jitsu Newaza (NEWAZA)

| From Category | Can Move Up To | Reason |
|---------------|----------------|---------|
| U8 | U10 | Allowed |
| U14 | U16 | Allowed |
| U21 | ADULT | Allowed |
| MASTERS | ADULT | Allowed |
| Others | ❌ No move-up | Rule restrictions |

### Groundfighting (GROUNDFIGHT)

| From Category | Can Move Up To | Reason |
|---------------|----------------|---------|
| U10 | U12 | Only move-up allowed |

**Note:** Groundfighting has very limited age categories and move-ups.

### Baby Monkeys Sumo (BABY_MONKEYS)

❌ **No age move-ups allowed** - This is the youngest category.

---

## Weight Move-Up Logic

### Basic Weight Move-Up
Athletes can move up to the next heavier weight class within their age category.

**Algorithm:**
1. Find athlete's current weight class (where weight ≤ limit)
2. Find next weight class in the array
3. If next weight exists → weight move-up available
4. If athlete is at max weight → plus weight (+) class available

### Plus Weight Move-Up
When an athlete is in a plus weight class (+X kg), they have special rules:

**For Age Move-Up with Plus Weight:**
1. Check if target age category has a higher weight class
2. If yes → suggest moving to next minus weight (-Y kg) where Y > X
3. If no → suggest same plus weight (+X kg) if target max weight = X

**Example:**
- Athlete: U18 MALE / (+)85 kg
- Age move-up to U21
- U21 has max weight 94 kg
- Suggestion: U21 / -94 kg (athlete must weigh ≤ 94 kg)

### Combined Age + Weight Move-Up
Athletes can combine age and weight move-ups:

**Rules:**
- Must be eligible for age move-up
- Must have weight move-up available in target category
- Warning shown: "⚠️ Verify athlete agrees to age and weight move-up"

**Example:**
- Current: U18 / -77 kg
- Age move-up available: U18 → U21
- Weight move-up in U18: -77 → -85 kg
- Check if -85 kg exists in U21: ✅ Yes
- Suggestion: U21 / -85 kg (age + weight move-up)

---

## Belt Progressions (Newaza Only)

Belts only apply to **Jiu-Jitsu Newaza (BJJ)** discipline. Belt is **optional**.

### Youth Belt Progression
```
White → Grey → Yellow → Orange → Blue → Purple → Brown → Black
```

### Adult/Masters Belt Progression
```
White → Blue → Purple → Brown → Black
```

### Special Belt: "ALL KIDS BELTS"
- Available for Youth categories only
- Means athlete can compete with any belt
- No belt move-ups when "ALL KIDS BELTS" is selected

### Belt Move-Up Logic
1. Athlete can move up to next belt in the progression
2. Belt move-ups can be combined with:
   - Same age, same weight (belt only)
   - Same age, weight move-up (belt + weight)
   - Age move-up, same weight (age + belt)
   - Age move-up, weight move-up (age + belt + weight)

**Example:**
- Current: Youth Jiu-Jitsu BJJ MALE / U10 / -32 kg / White
- Next belt: Grey
- Possible move-ups:
  1. U10 / -32 kg / Grey (belt only)
  2. U10 / -36 kg / Grey (belt + weight)
  3. U12 / -32 kg / White (age only)
  4. U12 / -36 kg / Grey (age + belt + weight)

---

## Smart Merge Optimizer Algorithm

The Smart Merge Optimizer analyzes single-athlete categories and suggests optimal merges to create competitive categories.

### Overview
**Goal:** Pair single athletes into the same category to enable competition.

**Input:** List of categories with only 1 athlete
**Output:** Recommended merge for each athlete

### Algorithm Steps

#### Step 1: Grouping
Group single athletes by:
- Discipline (FIGHTING, NEWAZA, GROUNDFIGHT, BABY_MONKEYS)
- Gender (MALE, FEMALE)
- Age category (U8, U10, U12, etc.)
- Entry (used to capture belt info for Newaza)

**Example Group Key:**
```
FIGHTING_MALE_U14_Youth Ju-Jitsu Fighting MALE / U14 / -48 kg
```

#### Step 2: Same-Category Weight Pairing
Within each group, try to pair athletes by weight:

1. Sort athletes by weight (ascending)
2. Try to pair adjacent weights using weight move-ups
3. Mark paired athletes as "used"

**Scoring:**
- Score = 100 - weightJump
- Smaller weight jumps get higher scores
- Example: 48kg → 52kg has score 96 (100 - 4)

**Example:**
```
Group: FIGHTING_MALE_U14
Athletes: [48kg, 52kg, 56kg]
Pairings:
  48kg can move to 52kg → Pair (score 96) ✅
  56kg has no partner → Continue to Step 3
```

#### Step 3: Find Best Merge for Unpaired Athletes
For each remaining single athlete:

1. **Generate all possible merges:**
   - Weight move-up (same age)
   - Age move-up (same/different weight)
   - Age + weight move-up
   - Belt move-ups (Newaza only)
   - Combinations of above

2. **Filter merges:**
   - Only include merges where target category has athletes
   - Check if weight class exists in target age category

3. **Score each merge type:**

#### Merge Type Scores

| Merge Type | Base Score | Notes |
|------------|------------|-------|
| Same age, weight move-up | 100 | Best option - minimal changes |
| Age move-up (same weight) | 80 | Good option |
| Age + weight move-up | 60 | Requires two changes |
| Belt move-up | 90 | Good for Newaza |
| Belt + weight move-up | 70 | |
| Age + belt move-up | 50 | |
| Age + belt + weight | 30 | Most complex, requires consent |

#### Step 4: Select Recommendation
For each athlete:
1. Sort available merges by score (highest first)
2. Select merge with highest score
3. Store as recommendation

**Reasons for No Merge:**
- "No athletes in weight move-up"
- "No athletes in age move-up to [category]"
- "Age move-up not allowed from [category]"
- "Weight class not available in target category"

### Smart Recommendations Display

In CSV results, when Smart Mode is enabled:

**For each category, show:**
- ✨ **Recommended merge** (highest scored option, highlighted)
- All other available merges (collapsed/expandable)
- Reasons why no merges if applicable

**Visual Indicators:**
- 🎯 Recommended merge shown first with distinct styling
- Score-based reasoning (e.g., "Best option: Weight progression")
- Warnings for complex merges (age + weight + belt)

---

## Merge Type Priority

When multiple merge options exist, they are prioritized as follows:

### Priority Order (Highest to Lowest)

1. **Weight Move-Up (Same Age)**
   - Reason: "Best option: Weight progression"
   - Least complex, most common

2. **Belt Move-Up (Newaza, Same Age, Same Weight)**
   - Reason: "Best option: Belt progression"
   - Natural progression in BJJ

3. **Age Move-Up (Same Weight)**
   - Reason: "Competing with older athletes in same weight"
   - Common practice

4. **Belt + Weight Move-Up**
   - Reason: "Belt progression with weight move-up"
   - Moderate complexity

5. **Age + Weight Move-Up**
   - Reason: "Requires age and weight move-up"
   - Requires verification

6. **Age + Belt Move-Up**
   - Reason: "Requires age and belt move-up"
   - Newaza only

7. **Age + Belt + Weight Move-Up**
   - Reason: "Requires age, belt and weight move-up"
   - Most complex, requires athlete consent
   - Warning: "⚠️ Verify athlete agrees to age, belt and weight move-up"

---

## Plus Weight Handling

Plus weights require special logic because they don't have a fixed upper limit.

### Identifying Plus Weight
```javascript
if (athlete.weight > maxWeightInCategory) {
  // Athlete is in plus weight class
  weightClass = "(+)" + maxWeightInCategory + " kg"
}
```

### Plus Weight Move-Up Rules

#### Scenario 1: Move to Higher Age Category with Higher Max Weight
```
Current: U18 MALE / (+)85 kg (weighs > 85kg)
Target: U21 MALE (max weight: 94 kg)
→ Suggest: U21 / -94 kg
Warning: "⚠️ Check weight ≤ 94kg"
```

#### Scenario 2: Move to Age Category with Same Max Weight
```
Current: U18 MALE / (+)85 kg
Target: U21 MALE (max weight: 94 kg, but next is also 94)
If U21 max = 85 kg:
→ Suggest: U21 / (+)85 kg (same plus weight)
```

#### Scenario 3: No Move-Up Available
```
Current: ADULT MALE / (+)94 kg
→ No age move-up available
→ No weight move-up available (already at max)
Result: No merges possible
```

---

## CSV Analysis Logic

### CSV File Requirements
Expected columns:
- `Firstname` - Athlete first name
- `Lastname` - Athlete last name
- `Birth` - Birth date (format: YYYY-MM-DD)
- `Gender` or `Gender ` - M/F or MALE/FEMALE
- `Weighin weight` - Athlete's weight
- `Entry` - Competition entry (contains discipline info)
- `AGE` - Registered age category
- `WEIGHT` - Registered weight class

### Processing Flow

1. **Parse CSV**
   - Read all rows
   - Skip rows marked "COACH PASS"
   - Normalize category keys (remove extra spaces, standardize format)

2. **Group by Category**
   - Key format: `"Discipline GENDER / AGE / WEIGHT / BELT"`
   - Example: `"Youth Ju-Jitsu Fighting MALE / U14 / -48 kg"`

3. **Find Single-Athlete Categories**
   - Filter categories with exactly 1 athlete
   - These are candidates for merging

4. **Analyze Merge Options**
   For each single athlete:
   - Determine discipline from `Entry` field
   - Calculate age from birth year
   - Find registered category
   - Generate all possible merges
   - Apply Smart Optimizer (if enabled)

5. **Categorize Results**
   - **With Merges:** Athletes with at least one merge option
   - **Age + Weight Only:** Athletes with only age/weight move-ups (no same-age weight moves)
   - **No Merges:** Athletes with no merge options

6. **Sort Results**
   Default sort order:
   1. Baby Monkeys (Boys, Girls)
   2. Groundfighting (Male, Female)
   3. Youth Fighting MALE (U8-U18)
   4. Youth Fighting FEMALE (U8-U18)
   5. Youth Jiu-Jitsu BJJ MALE (U8-U18)
   6. Youth Jiu-Jitsu BJJ FEMALE (U8-U18)
   7. Adult Fighting MALE (U21+)
   8. Adult Fighting FEMALE (U21+)
   9. Adult Jiu-Jitsu BJJ MALE (U21+)
   10. Adult Jiu-Jitsu BJJ FEMALE (U21+)
   11. Within each section: By age (U8 → U10 → ... → MASTERS)
   12. Within each age: By weight (ascending)

### Display Tabs

Results are shown in tabs:

1. **Smart Merges Tab** (if Smart Mode enabled)
   - Shows recommended merge for each athlete
   - Highlights best option
   - Expandable to see all options

2. **All Available Merges Tab**
   - Shows all athletes with any merge option
   - All merge options listed

3. **Age + Weight Only Tab**
   - Athletes with age/weight move-ups but no same-age weight moves
   - Typically require more validation

4. **No Merges Tab**
   - Athletes with no merge options
   - Shows reason why (e.g., "Age move-up not allowed")

---

## Validation Rules

### Age Validation
- Age must be within category range
- Masters (35+) takes priority over Adult
- Birth year and event year must be provided

### Weight Validation
- Weight must be > 0 kg
- Weight must be ≤ 200 kg (sanity check)
- Weight determines weight class assignment

### Discipline Validation
- Must be one of: FIGHTING, NEWAZA, GROUNDFIGHT, BABY_MONKEYS
- Detected from entry text (keywords: "fighting", "newaza", "bjj", "groundfight", "baby monkey")

### Belt Validation (Newaza Only)
- Belt is optional
- If provided, must be valid belt color
- Belt progression must follow Youth vs Adult rules
- "ALL KIDS BELTS" only for Youth categories

### Move-Up Validation
- Age move-up must be in MOVE_UP configuration
- Target age category must exist
- Target weight class must exist in target category
- Plus weight athletes need special verification

---

## Example Scenarios

### Example 1: Simple Weight Move-Up
```
Athlete: John Smith
Entry: Youth Ju-Jitsu Fighting MALE / U14 / -48 kg
Competition has:
  - No other athletes in U14 / -48 kg
  - 2 athletes in U14 / -52 kg

Smart Recommendation:
  → Youth Ju-Jitsu Fighting MALE / U14 / -52 kg
  Reason: "Best option: Weight progression"
  Score: 96 (100 - 4kg jump)
```

### Example 2: Age Move-Up Required
```
Athlete: Jane Doe
Entry: Youth Jiu-Jitsu BJJ FEMALE / U14 / -44 kg / Blue
Competition has:
  - No athletes in U14 / -44 kg
  - No athletes in U14 / -48 kg (weight move-up)
  - 3 athletes in U16 / -44 kg

Smart Recommendation:
  → Youth Jiu-Jitsu BJJ FEMALE / U16 / -44 kg / Blue
  Reason: "Competing with older athletes in same weight"
  Score: 80 (age move-up)
```

### Example 3: Complex Merge (Age + Weight + Belt)
```
Athlete: Mike Johnson
Entry: Youth Jiu-Jitsu BJJ MALE / U10 / -32 kg / White
Competition has:
  - No athletes in U10 / any weight
  - 2 athletes in U12 / -36 kg / Grey

Available Merges:
  1. U12 / -32 kg / White (age only) - No athletes ❌
  2. U12 / -36 kg / White (age + weight) - No athletes ❌
  3. U10 / -36 kg / Grey (weight + belt) - No athletes ❌
  4. U12 / -36 kg / Grey (age + weight + belt) - 2 athletes ✅

Smart Recommendation:
  → Youth Jiu-Jitsu BJJ MALE / U12 / -36 kg / Grey
  Reason: "Requires age, belt and weight move-up"
  Score: 30 (complex merge)
  Warning: "⚠️ Verify athlete agrees to age, belt and weight move-up"
```

### Example 4: No Merges Available
```
Athlete: Sarah Lee
Entry: Ju-Jitsu Fighting FEMALE / ADULT / (+)70 kg

Reasons for No Merge:
  - No age move-up from ADULT (except Masters can go to Adult)
  - No weight move-up (already in plus weight, max weight)
  - No athletes in ADULT / (+)70 kg

Result: No merge options available
```

### Example 5: Baby Monkeys Sumo
```
Athlete: Tommy (age 4)
Entry: Baby Monkeys Sumo - Boys / 3 to 4 years / -20 kg
Competition has:
  - 1 athlete in Boys / -20 kg (Tommy)
  - 2 athletes in Boys / (+)20 kg

Available Merge:
  → Baby Monkeys Sumo - Boys / 3 to 4 years / (+)20 kg
  Reason: "Best option: Weight progression"
  (No age move-ups available for Baby Monkeys)
```

---

## Technical Implementation Notes

### Key Functions

1. **`ageReached(birthYear, eventYear)`**
   - Returns age athlete will be during competition year

2. **`getAgeObj(age)`**
   - Returns age category object based on age
   - Prioritizes MASTERS for age ≥ 35

3. **`allowedAgeCategories(ageObj, disciplineKey)`**
   - Returns array of [base category, move-up category] if available
   - Uses MOVE_UP configuration

4. **`getOwnAndUpWeightClasses(weight, weightTable)`**
   - Returns `{ own, up }` object
   - `own`: Current weight class (minus or plus)
   - `up`: Next weight class if available

5. **`findMergeOptions(athlete, currentYear, allCategories)`**
   - Main function to find all possible merges
   - Returns `{ merges: [], reasons: [] }`
   - Checks all combinations of age/weight/belt move-ups

6. **`optimizeMerges(singleCategories, categories, currentYear)`**
   - Smart optimizer algorithm
   - Returns recommendations object: `{ category: { merge, score, reason } }`

7. **`formatCategoryLabel(cat, gender, disciplineKey)`**
   - Formats category label based on discipline
   - Handles "Boys"/"Girls" for Baby Monkeys
   - Handles "Youth" prefix and belt labels

8. **`formatAgeDisplay(ageCategory)`**
   - Formats age display for output
   - MASTERS → "MASTER 35+"
   - BABY_MONKEYS → "3 to 4 years"
   - Others → Category name as-is

### Category String Format

Standard format:
```
[Discipline Label] [GENDER] / [AGE] / [WEIGHT] / [BELT (optional)]
```

Examples:
```
Youth Ju-Jitsu Fighting MALE / U14 / -48 kg
Jiu-Jitsu BJJ FEMALE / ADULT / -57 kg / Blue
Groundfight MALE / U12 / -28 kg
Baby Monkeys Sumo - Boys / 3 to 4 years / (+)20 kg
```

---

## Version History

- **2.10.3** - Fixed age + weight move-up showing wrong weight class
- **2.10.2** - Fixed Baby Monkeys age display, relocated belt selector
- **2.10.1** - Added Baby Monkeys to MOVE_UP config
- **2.10.0** - Added Baby Monkeys Sumo discipline
- **2.9.20** - Added U8 weight classes
- **2.9.x** - Smart merge optimizer, tab-based UI
- **Earlier** - Initial development, CSV analysis, basic eligibility checking

---

## Contact & Support

For questions about JJIF rules or competition regulations, please consult the official JJIF Competition Rulebook.

This system implements rules based on JJIF standards as of January 2025.

---

**End of Documentation**
