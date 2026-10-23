# TypeScript

Holberton School `web_react` project: TypeScript basics (interfaces, classes, functions, the DOM, namespaces, declaration merging, ambient declarations, and nominal typing).

Each `task_N` folder is its own small project with its own `package.json`. You run every task the same way.

---

## 1. Before you start

You need **Node.js** (version 18 or newer) and **npm**. Check with:

```bash
node -v
```

If you see a version number like `v20.x.x`, you are ready.

---

## 2. Run any task in 4 steps

The example uses `task_1`. Swap in any task folder.

**Step 1. Go into the task folder**

```bash
cd TypeScript/task_1
```

**Step 2. Install the dependencies** (only the first time, takes about a minute)

```bash
npm install
```

**Step 3. Pin the Node type definitions** (only the first time)

```bash
npm install --no-save @types/node@18
```

> Why: `package.json` uses TypeScript 4.9, but `npm install` grabs the newest `@types/node`, which needs TypeScript 5. Without this step you get errors coming from `node_modules/@types/node/...`. The `--no-save` flag leaves `package.json` unchanged.

**Step 4. Start the app**

```bash
npm run start-dev
```

Your browser opens at **http://localhost:8080**. In the terminal, look for:

```
No typescript errors found.
```

To see what the code prints, open the browser console:

- **Chrome / Edge on Mac:** `Cmd + Option + J`
- **Chrome / Edge on Windows/Linux:** `Ctrl + Shift + J` or `F12`
- **Safari:** enable *Develop* menu in Settings → Advanced, then `Cmd + Option + C`

The page reloads by itself every time you save a `.ts` file. Press `Ctrl + C` in the terminal to stop the server.

---

## 3. Command cheat sheet

Run these from inside a task folder.

| Command | What it does |
| ------- | ------------ |
| `npm run start-dev` | Starts a live dev server at http://localhost:8080 and reloads when you save |
| `npm run build` | Type-checks and bundles the code into `dist/` (look for `compiled successfully` and no `ERROR` lines) |
| `npx tsc --noEmit -p .` | Type-checks only, no output files (prints nothing or `No errors found` when everything is fine) |

---

## 4. Task-by-task guide

### Task 0: Student table (`task_0`)

**What it is:** a `Student` interface, two students in a `studentsList` array, and a table built with vanilla DOM code.

**Run it:** `npm run start-dev`

**What you should see on the page:**

| | |
| --- | --- |
| John | San Francisco |
| Jane | Los Angeles |

**Try it yourself:** in `js/main.ts`, add a third student and push it into `studentsList`. Save, and a new row appears.

---

### Tasks 1 to 4: Teacher, Directors, printTeacher, StudentClass (`task_1`)

**What it is:**
- `Teacher` interface: `firstName`/`lastName` are `readonly`, `yearsOfExperience` is optional, and any extra property (like `contract`) is allowed
- `Directors` interface that extends `Teacher` and adds `numberOfReports`
- `printTeacher` function (described by the `printTeacherFunction` interface)
- `StudentClass`, whose shape and constructor are both described by interfaces

**Run it:** `npm run start-dev`, then open the browser console.

**What you should see in the console:**

```
{firstName: 'John', fullTimeEmployee: false, lastName: 'Doe', location: 'London', contract: false}
{firstName: 'John', lastName: 'Doe', location: 'London', fullTimeEmployee: true, numberOfReports: 17}
J. Doe
```

**Try it yourself:**

1. See `StudentClass` working. Add this at the bottom of `js/main.ts` and save:
   ```ts
   const student = new StudentClass('Ana', 'Lopez');
   console.log(student.displayName());     // Ana
   console.log(student.workOnHomework());  // Currently working
   ```
2. Break it on purpose. Add this line and save:
   ```ts
   teacher3.firstName = 'Jane';
   ```
   The terminal shows: `Cannot assign to 'firstName' because it is a read-only property.` Delete the line to fix it.

---

### Tasks 5 to 7: Director/Teacher classes, executeWork, teachClass (`task_2`)

**What it is:**
- `Director` and `Teacher` classes implementing `DirectorInterface` and `TeacherInterface`
- `createEmployee(salary)`: returns a `Teacher` only if `salary` is a number under 500, otherwise a `Director`
- `isDirector` type predicate and `executeWork`
- `Subjects` string literal type (`'Math' | 'History'`) and `teachClass`

**Run it:** `npm run start-dev`, then open the browser console.

**What you should see in the console:**

```
Teacher {}
Director {}
Director {}
Getting to work
Getting to director tasks
Teaching Math
Teaching History
```

**Try it yourself:**

1. Change `createEmployee(200)` to `createEmployee(499)`, then `500`, and watch `Teacher` switch to `Director`.
2. Break it on purpose. Add this line and save:
   ```ts
   teachClass('Science');
   ```
   The terminal shows: `Argument of type '"Science"' is not assignable to parameter of type 'Subjects'.`

---

### Task 8: Ambient declarations for crud.js (`task_3`)

**What it is:**
- `js/interface.ts`: the `RowID` type and the `RowElement` interface
- `js/crud.js`: a plain JavaScript "library" with no types
- `js/crud.d.ts`: an ambient file that adds types to `crud.js`
- `js/main.ts`: inserts, updates and deletes a row using the typed library

**Run it:** `npm run start-dev`, then open the browser console.

**What you should see in the console** (the row id is random, so your number will differ):

```
Insert row {firstName: 'Guillaume', lastName: 'Salva'}
Update row 482 {firstName: 'Guillaume', lastName: 'Salva', age: 23}
Delete row id 482
```

**Try it yourself:** in your editor (VS Code), hover over `CRUD.insertRow` in `main.ts`. The tooltip shows `insertRow(row: RowElement)`. Those types come from `crud.d.ts`. Then try `CRUD.insertRow({ firstName: 'Ana' })`: it fails because `lastName` is missing.

---

### Task 9: Namespace and declaration merging (`task_4`)

**What it is:** a `Subjects` namespace spread across several files:
- `Teacher.ts`: the base `Teacher` interface
- `Subject.ts`: the `Subject` class with a `teacher` and `setTeacher()`
- `Cpp.ts`, `React.ts`, `Java.ts`: each one *merges* a new optional field into `Teacher` (`experienceTeachingC`, `experienceTeachingReact`, `experienceTeachingJava`) and adds a class

> This task has no `main.ts` yet, so `npm run start-dev` and `npm run build` fail here. That is expected. Use the commands below.

**Check the types:**

```bash
npx tsc --noEmit -p .
```

**See it run** (compile the namespace to a single file, then run it with Node):

```bash
npx tsc --outFile dist/subjects.js --target es5 --skipLibCheck js/subjects/*.ts
```

```bash
node -e "$(cat dist/subjects.js)
const teacher = { firstName: 'Guillaume', lastName: 'Salva', experienceTeachingC: 10 };
const cpp = new Subjects.Cpp();
cpp.setTeacher(teacher);
console.log(cpp.getRequirements());
console.log(cpp.getAvailableTeacher());
const java = new Subjects.Java();
java.setTeacher(teacher);
console.log(java.getAvailableTeacher());"
```

**What you should see:**

```
Here is the list of requirements for Cpp
Available Teacher: Guillaume
No available teacher
```

`Java` says "No available teacher" because the teacher has no `experienceTeachingJava`. Add `experienceTeachingJava: 5` to the teacher and run the `node` command again.

---

### Task 10: Brand convention and nominal typing (`task_5`)

**What it is:** `MajorCredits` and `MinorCredits` both have `credits: number`, plus a unique "brand" property (`_majorCreditsBrand` / `_minorCreditsBrand`). Without the brand, TypeScript would treat them as the same type. With it, you can't mix them up. `sumMajorCredits` and `sumMinorCredits` add credits together.

**Run it:**

```bash
npx tsc --noEmit -p .
```

No output (or `No errors found`) means it compiles cleanly. Nothing is printed to the console in this task.

**Try it yourself:** add this to the bottom of `js/main.ts`:

```ts
const minor = { credits: 2 } as MinorCredits;
sumMajorCredits(minor, minor);
```

Run the type check again. You get:
`Argument of type 'MinorCredits' is not assignable to parameter of type 'MajorCredits'.`
That error is the brand doing its job. Delete the lines when you're done.

---

## 5. Troubleshooting

| Problem | Fix |
| ------- | --- |
| Errors that point into `node_modules/@types/node/...` | Run step 3: `npm install --no-save @types/node@18` |
| `Port 8080 is already in use` | Stop the other server with `Ctrl + C`, or run `npm run start-dev -- --port 8081` |
| `npm run start-dev` fails in `task_4` | Expected: there is no `main.ts`. Use the commands in the Task 9 section |
| The browser doesn't open by itself | Go to http://localhost:8080 manually |
| `command not found: npm` | Install Node.js from https://nodejs.org (npm comes with it) |
