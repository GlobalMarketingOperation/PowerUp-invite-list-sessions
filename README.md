<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Join the next session</title>
  <style>
    :root {
      --green: #00A651;
      --green-dark: #00843F;
      --light: #F2F8F4;
      --text: #1E1E1E;
      --error: #C62828;
    }
    * { box-sizing: border-box; }
    body {
      margin: 0;
      font-family: "Segoe UI", Arial, sans-serif;
      background: var(--light);
      color: var(--text);
      display: flex;
      justify-content: center;
      padding: 24px 16px;
    }
    .card {
      background: #fff;
      max-width: 520px;
      width: 100%;
      border-radius: 12px;
      border-top: 6px solid var(--green);
      box-shadow: 0 4px 16px rgba(0,0,0,0.08);
      padding: 28px 24px;
    }
    h1 { font-size: 1.4rem; margin: 0 0 8px; }
    p  { margin: 0 0 20px; line-height: 1.5; }
    label { display: block; font-weight: 600; margin-bottom: 6px; }
    input[type="email"] {
      width: 100%;
      padding: 12px;
      font-size: 1rem;
      border: 1px solid #ccc;
      border-radius: 8px;
    }
    input[type="email"]:focus {
      outline: 3px solid rgba(0,166,81,0.3);
      border-color: var(--green);
    }
    button {
      margin-top: 16px;
      width: 100%;
      padding: 12px;
      font-size: 1rem;
      font-weight: 600;
      color: #fff;
      background: var(--green);
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }
    button:hover { background: var(--green-dark); }
    button:disabled { background: #9E9E9E; cursor: not-allowed; }
    .msg { margin-top: 14px; font-weight: 600; min-height: 1.2em; }
    .msg.ok { color: var(--green-dark); }
    .msg.err { color: var(--error); }
  </style>
</head>
<body>
  <main class="card">
    <h1>Not receiving the session invites?</h1>
    <p>Share your email below and we'll add you to the invite list for the next session.</p>

    <form id="joinForm" novalidate>
      <label for="email">Your email</label>
      <input type="email" id="email" name="email" placeholder="name@company.com" required />
      <button type="submit" id="submitBtn">Add me to the next session</button>
      <div id="msg" class="msg" aria-live="polite"></div>
    </form>
  </main>

  <script>
    // Paste your Power Automate HTTP trigger URL here
    const FLOW_URL = "(https://0458ee4aa906e0628640d9914f2d48.4c.environment.api.powerplatform.com:443/powerautomate/automations/direct/cu/17/workflows/bf650759845548daa201263c93371331/triggers/manual/paths/invoke?api-version=1&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=GQadXVKWVKpk-ap5jqZakCTxwbb4ukgtz9v1vKotIUM)";

    const form = document.getElementById("joinForm");
    const emailInput = document.getElementById("email");
    const btn = document.getElementById("submitBtn");
    const msg = document.getElementById("msg");

    function showMsg(text, type) {
      msg.textContent = text;
      msg.className = "msg " + type;
    }

    form.addEventListener("submit", async (e) => {
      e.preventDefault();
      const email = emailInput.value.trim().toLowerCase();
      const valid = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);

      if (!valid) {
        showMsg("Please enter a valid email address.", "err");
        return;
      }

      btn.disabled = true;
      btn.textContent = "Sending...";
      showMsg("", "");

      try {
        const res = await fetch(FLOW_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            email: email,
            submittedAt: new Date().toISOString(),
            source: "GitHub session sign-up"
          })
        });

        if (!res.ok) throw new Error("Request failed");

        showMsg("Thank you! You'll get the invite for the next session.", "ok");
        form.reset();
      } catch (err) {
        showMsg("Something went wrong. Please try again later.", "err");
      } finally {
        btn.disabled = false;
        btn.textContent = "Add me to the next session";
      }
    });
  </script>
</body>
</html>
