# The-Notamedia-MVP-Hackathon-2025

Excellent — let’s go **full pro-level detail** with *descriptions, explanations, and starter code snippets* for each step.

This is a big one, so buckle up! We’ll cover **what to do, why, and give you a strong starting point for each step**.

---

# 🚀 **AI Code Reviewer & Tutor — Detailed 2-Week Build Plan**

---

## ⚡ **WEEK 1: BUILD CORE MVP**

---

### ✅ **Day 1 — Kickoff, Repo, Boilerplate**

#### 🎯 Description

* Pick final name & branding (e.g., **CodeSensei**).
* Create a new **public GitHub repo** to track progress transparently.
* Initialize **frontend** and **backend** structure.
* Verify you can call Notamedia GenAI API (test minimal prompt).

#### 💡 Explanation

Setting up properly saves you from the “week 2 panic refactor.” Testing API connection early ensures you won’t get stuck later.

---

#### 🗂️ **File/Folder Structure Example**

```plaintext
codesensei/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── App.jsx
│   │   └── main.jsx
│   └── package.json
├── backend/
│   ├── index.js
│   └── package.json
└── README.md
```

---

#### 💻 **Starter Code**

##### Backend setup (Node + Express)

```bash
mkdir backend
cd backend
npm init -y
npm install express axios cors dotenv
```

Create `.env` in backend:

```plaintext
NOTAMEDIA_API_KEY=your_api_key_here
```

`backend/index.js`

```js
import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';
import axios from 'axios';

dotenv.config();
const app = express();
app.use(cors());
app.use(express.json());

app.post('/api/review', async (req, res) => {
  const { code } = req.body;

  try {
    const response = await axios.post(
      'https://api.notamedia.ai/v1/genai',
      {
        prompt: `Please review this code:\n\n${code}\n\nGive concise issues, explanations, and suggest improvements.`,
      },
      {
        headers: {
          Authorization: `Bearer ${process.env.NOTAMEDIA_API_KEY}`,
        },
      }
    );

    res.json({ review: response.data.choices[0].message.content });
  } catch (err) {
    console.error(err);
    res.status(500).json({ error: 'Failed to fetch review' });
  }
});

app.listen(5000, () => {
  console.log('Backend server running on port 5000');
});
```

---

##### Frontend setup (Vite + React + Tailwind)

```bash
npm create vite@latest frontend -- --template react
cd frontend
npm install
npm install axios @monaco-editor/react
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Update `tailwind.config.js`:

```js
export default {
  content: ['./index.html', './src/**/*.{js,jsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

In `src/index.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

---

### ✅ **Day 2 — UI Skeleton & Code Input**

#### 🎯 Description

* Create basic UI structure.
* Add code editor (Monaco).
* Add "Review My Code" button.
* Add placeholder for results.

---

#### 💡 Explanation

We build a basic *end-to-end* flow early to avoid last-minute surprise integrations. Monaco makes it look pro immediately.

---

#### 💻 **Starter Code**

`src/App.jsx`

```jsx
import { useState } from 'react';
import Editor from '@monaco-editor/react';
import axios from 'axios';

function App() {
  const [code, setCode] = useState('// Paste your code here');
  const [review, setReview] = useState('');

  const handleReview = async () => {
    const res = await axios.post('http://localhost:5000/api/review', { code });
    setReview(res.data.review);
  };

  return (
    <div className="p-6 bg-gray-100 min-h-screen">
      <h1 className="text-3xl font-bold mb-4">CodeSensei - AI Code Reviewer</h1>

      <Editor
        height="300px"
        language="javascript"
        value={code}
        onChange={(value) => setCode(value)}
        theme="vs-dark"
      />

      <button
        className="mt-4 px-4 py-2 bg-blue-600 text-white rounded"
        onClick={handleReview}
      >
        Review My Code
      </button>

      {review && (
        <div className="mt-6 bg-white p-4 rounded shadow">
          <h2 className="text-xl font-semibold mb-2">AI Review</h2>
          <pre>{review}</pre>
        </div>
      )}
    </div>
  );
}

export default App;
```

---

---

### ✅ **Day 3 — First GenAI Code Review Integration**

#### 🎯 Description

* Finalize and improve your initial prompt.
* Integrate actual Notamedia review.
* Display review nicely.

---

#### 💡 Explanation

Here, you ensure your AI actually provides meaningful, actionable feedback. Prompt tuning is key — experiment with clarity.

---

#### 💻 **Prompt Example**

```plaintext
"Please act as a senior software engineer. Review this code snippet. List concise issues, potential bugs, and improvements. Then explain each issue in simple language so a beginner can understand. Finally, suggest an overall improvement strategy."
```

> 🔥 Save this prompt in a separate file so you can iterate without digging into code each time.

---

---

### ✅ **Day 4 — Simple Explanations Section**

#### 🎯 Description

* Separate "simple explanations" from technical issues.
* Use different UI blocks/cards for each.

---

#### 💡 Explanation

Making explanations explicit helps beginners — and makes the app feel more educational rather than purely corrective.

---

#### 💻 **Code Update**

Split `review` into sections using parsing logic or modify prompt to output separate sections using markers (e.g., "## Issues", "## Explanations", "## Improvements").

```js
const sections = review.split("##");
```

Then render each section separately:

```jsx
{sections.map((section, idx) => (
  <div key={idx} className="mb-4 bg-gray-50 p-2 rounded">
    <pre>{section}</pre>
  </div>
))}
```

---

---

### ✅ **Day 5 — Suggested Fixes & Improved Code**

#### 🎯 Description

* Update prompt to generate improved code block.
* Add "Copy Code" button.

---

#### 💡 Explanation

Instantly usable suggestions build trust. The "copy" button is a small detail with big UX impact.

---

#### 💻 **Prompt Extension**

```plaintext
"After reviewing, provide a possible improved version of the code, fully rewritten if necessary, with clear inline comments explaining each change."
```

Frontend:

```jsx
<button
  className="mt-2 px-3 py-1 bg-green-500 text-white rounded"
  onClick={() => navigator.clipboard.writeText(improvedCode)}
>
  Copy Improved Code
</button>
```

---

---

### ✅ **Day 6 — Interactive "Ask Why"**

#### 🎯 Description

* Let user click on any issue to ask "Why?"
* Send clicked text back to backend for further GenAI explanation.

---

#### 💡 Explanation

Adds an extra layer of engagement. Shows judges you went beyond static feedback.

---

#### 💻 **Code Snippet**

```jsx
<div
  className="cursor-pointer text-blue-600"
  onClick={() => handleWhy(section)}
>
  {section}
</div>
```

Backend handler example:

```js
app.post('/api/explain', async (req, res) => {
  const { issue } = req.body;

  const prompt = `Explain in detail why this issue is a problem: "${issue}". Use simple terms and provide an example fix.`;

  const response = await axios.post(...);
  res.json({ explanation: response.data.choices[0].message.content });
});
```

---

---

### ✅ **Day 7 — Difficulty & Learning Path**

#### 🎯 Description

* AI assesses skill level.
* Suggests next topics (e.g., "Focus on design patterns", "Improve error handling").

---

#### 💡 Explanation

Turns a tool into a mentor. Adds educational value beyond code correctness.

---

#### 💻 **Prompt Addition**

```plaintext
"Finally, estimate the developer's skill level (Beginner, Intermediate, Advanced). Suggest three focus areas they should study next to improve."
```

Display in frontend:

```jsx
<div className="mt-6 p-4 bg-yellow-50 border-l-4 border-yellow-500">
  <h3 className="font-bold">Skill Assessment & Learning Path</h3>
  <p>{skillAssessment}</p>
</div>
```

---

## ⚡ **WEEK 2: POLISH, TEST & LAUNCH**

---

### ✅ **Day 8 — UI Polishing**

* Add animations (e.g., loading spinners while AI is thinking).
* Consistent spacing, colors, typography.
* Mobile responsiveness.

---

### ✅ **Day 9 — Edge Cases & Errors**

* Show friendly messages on API fail.
* Prevent empty submissions.
* Limit very large code pastes.

---

### ✅ **Day 10 — Save/Load Sessions (Bonus)**

* Save last review to local storage.

```js
useEffect(() => {
  localStorage.setItem('lastReview', JSON.stringify(reviewData));
}, [reviewData]);

useEffect(() => {
  const saved = localStorage.getItem('lastReview');
  if (saved) setReviewData(JSON.parse(saved));
}, []);
```

---

### ✅ **Day 11 — Analytics (Optional)**

* Simple "session count" metric.
* Logs for demo showcase.

---

### ✅ **Day 12 — Deployment**

* Deploy frontend (Vercel, Netlify).
* Deploy backend (Render, Railway, or fly.io).

---

### ✅ **Day 13 — Record Demo Video**

* Script: Show code paste → review → click "Why?" → copy improved code → show learning path.
* Use Loom or OBS.

---

### ✅ **Day 14 — Final Polish & Submission**

* README with screenshots, features, and setup.
* Submit to Notamedia before 9:15pm GMT+3.

---

# ✅ **🎁 Final Bonuses**

I can also prepare for you:

✅ Full **prompt templates** (copy-ready).
✅ A **wireframe/Figma mockup** if you'd like visuals.
✅ A **complete repo starter package** (folder tree + minimal working code zipped).

---



🎯 **Outstanding! Let’s finalize your prompts and wireframes today.** Here’s a detailed, structured, pro-level package for you.

---

# 💬 **1️⃣ Full Notamedia Prompt Templates (Copy-Ready)**

## ✅ ✨ **Main Review Prompt**

```
Act as a senior software engineer. Review the following code snippet carefully.

1. List concise issues and potential bugs.
2. Provide clear explanations for each issue in simple terms that a beginner can understand.
3. Suggest an overall improvement strategy.
4. Finally, generate a possible improved version of the code. Include inline comments explaining each change.

Code snippet:
---
{{user_code}}
---
```

---

## ✅ 💡 **"Why" Clarification Prompt**

```
Explain in detail why this specific issue is a problem in code:

"{{clicked_issue}}"

Use simple language. If possible, give a practical example of how to fix or improve it.
```

---

## ✅ 🎓 **Skill Assessment & Learning Path Prompt**

```
Based on this code snippet and its issues, estimate the developer's skill level (Beginner, Intermediate, or Advanced).

Then suggest three learning topics or areas they should focus on next to improve their skills.

Code snippet:
---
{{user_code}}
---
```

---

## ✅ 🛠️ **Improved Code-Only Prompt (Optional — if you want a separate "improve only" endpoint)**

```
Rewrite the following code snippet to improve quality and readability. Fix any potential bugs and include inline comments explaining the important changes.

Code snippet:
---
{{user_code}}
---
```

---

### 🟢 **Notes for prompts**

* The `{{user_code}}` placeholder is where you'll inject the pasted code in your backend.
* Always explicitly instruct the model to use simple terms if you want beginner-friendly feedback.
* Mark sections with titles or "## Section Name" if you want to split responses easily on frontend.

---

# 🧑‍🎨 **2️⃣ Wireframe / UI Flow Sketch**

I’ve designed this as a clear flow that maximizes "wow" moments in your demo.

---

## 💻 **Main Page Layout**

```
+------------------------------------------------+
|           CodeSensei - AI Code Reviewer        |
+------------------------------------------------+
| [Code Editor (Monaco)]                         |
|                                                |
| [ Review My Code Button ]                      |
+------------------------------------------------+
| [ AI Review Panel ]                            |
|   - Issues (clickable)                         |
|   - Simple Explanations                        |
|   - Improved Code (copy button)                |
|   - Skill Level & Learning Suggestions         |
+------------------------------------------------+
```

---

## 🔥 **Click Flow**

```
[Click on Issue] → [Show modal with "Why" explanation] → [User closes modal]
```

---

## ✅ **Key Elements**

* **Code Editor**: Monaco (VS Code-like).

* **Action Button**: Primary CTA to trigger review.

* **AI Review Panel**:

  * Issues (render as a clickable list).
  * Explanations (render separately).
  * Improved Code (block + "Copy" button).
  * Learning suggestions (highlighted as a card or banner).

* **Loading state**: Spinners or "AI thinking..." animations.

* **Error states**: Friendly messages.

---

## 🎨 **Color & Visual Style Suggestion**

* Dark code editor theme (vs-dark).
* Clean, light UI background to keep focus on results.
* Tailwind colors: Blue accents (`blue-600`), green for success (`green-500`), yellow for warnings (`yellow-500`).

---

# 🗂️ **3️⃣ Suggested Folder / Component Structure**

```
frontend/
├── src/
│   ├── components/
│   │   ├── CodeEditor.jsx
│   │   ├── ReviewPanel.jsx
│   │   ├── IssuesList.jsx
│   │   ├── ExplanationModal.jsx
│   │   └── SkillCard.jsx
│   ├── pages/
│   │   └── Home.jsx
│   ├── App.jsx
│   └── main.jsx
```

---

## ✅ **Component Explanations**

* `CodeEditor.jsx`: Monaco editor wrapper.
* `ReviewPanel.jsx`: Main container for AI feedback sections.
* `IssuesList.jsx`: Renders clickable issues.
* `ExplanationModal.jsx`: Modal for "Why" details.
* `SkillCard.jsx`: Skill level and learning suggestions.

---

# 💥 **4️⃣ Next Step: Build Out Starter Code**

If you'd like, I can also prepare **starter code files for each component**, ready to drop into your repo, including Tailwind classes and handlers.

---

## 🚀 ✅ *Shall I generate those starter code files for you as a next step?*

Say **"Yes, create starter code for components"**, and I’ll send you each file in one big bundle — so you can copy-paste, run `npm install`, and fly.

*We’re building this faster than a keyboard warrior on energy drinks!* 💻⚡🔥


## 💥 **Next step: Shall I draft the full Notamedia prompt templates and wireframes for you right now?**

Reply *"Yes, let’s finalize prompts and wireframes"* — and we’ll get you moving like a rocket on Red Bull. 🚀👨‍💻
