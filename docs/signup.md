# QEMU 训练营报名

欢迎报名 QEMU 训练营。当前报名只需要确认 GitHub 用户名。系统会以报名表中填写的 GitHub 用户名作为后续实验仓库的授权账号。

!!! warning "公开信息提醒"

    当前报名表通过 GitHub Issue Form 提交，报名内容会出现在公开 Issue 中。请不要填写手机号、邮箱、身份证号等隐私信息。

<form id="signup-form" class="signup-form">
  <label for="github-username">GitHub 用户名</label>
  <div class="signup-form__row">
    <input
      id="github-username"
      name="github_username"
      type="text"
      autocomplete="username"
      maxlength="39"
      pattern="[A-Za-z0-9](?:[A-Za-z0-9-]{0,37}[A-Za-z0-9])?"
      placeholder="例如：bbbbbq"
      required
    />
    <button type="submit" class="md-button md-button--primary">立即报名</button>
  </div>
  <p id="signup-status" class="signup-form__status" aria-live="polite" hidden></p>
</form>

<noscript>
  <p>
    <a class="md-button md-button--primary" href="https://github.com/gevico/qemu-camp-tutorial/issues/new?template=signup.yml">立即报名</a>
  </p>
</noscript>

!!! note "报名后会发生什么"

    点击“立即报名”后会跳转到 GitHub Issue Form。请在 GitHub 页面确认并提交 Issue。

    报名 Issue 会带有 `[报名]` 标题前缀和 `signup` 标签，后续可由报名仓库或子仓库中的 CI/CD 识别并执行自动化流程，包括记录报名信息、创建或 fork 实验仓库，并为报名账号配置仓库权限。

!!! tip "填写前请确认"

    - 已登录自己的 GitHub 账号。
    - 填写的 GitHub 用户名就是后续完成实验和提交作业的账号。
    - 如果需要更换 GitHub 账号，请重新使用正确账号提交报名。

<script>
(() => {
  const form = document.getElementById("signup-form");
  const input = document.getElementById("github-username");
  const status = document.getElementById("signup-status");
  const submitButton = form?.querySelector("button[type='submit']");

  if (!form || !input) {
    return;
  }

  const setStatus = (message, kind = "info", link) => {
    if (!status) {
      return;
    }

    status.hidden = false;
    status.className = `signup-form__status signup-form__status--${kind}`;
    status.textContent = message;

    if (link) {
      status.append(" ");
      const anchor = document.createElement("a");
      anchor.href = link;
      anchor.textContent = "查看已有报名";
      anchor.rel = "noopener";
      status.append(anchor);
    }
  };

  const issueMentionsUsername = (issue, username) => {
    const normalizedUsername = username.toLowerCase();
    const title = issue.title?.trim().toLowerCase() ?? "";
    const body = issue.body?.toLowerCase() ?? "";
    const usernamePattern = new RegExp(`(^|[^a-z0-9-])${normalizedUsername.replace(/[.*+?^${}()|[\]\\]/g, "\\$&")}([^a-z0-9-]|$)`);

    return usernamePattern.test(title) || usernamePattern.test(body);
  };

  const isIgnoredSignupIssue = (issue) => {
    const ignoredLabels = new Set(["signup/invalid", "signup/duplicate"]);
    const hasIgnoredLabel = (issue.labels ?? []).some((label) => ignoredLabels.has(label.name ?? label));

    return Boolean(issue.pull_request) || hasIgnoredLabel;
  };

  const findExistingSignup = async (username) => {
    let apiUrl = "https://api.github.com/repos/gevico/qemu-camp-tutorial/issues?state=all&labels=signup&per_page=100";
    let pageCount = 0;

    while (apiUrl && pageCount < 10) {
      pageCount += 1;
      const response = await fetch(apiUrl, {
        headers: {
          Accept: "application/vnd.github+json",
        },
      });

      if (!response.ok) {
        throw new Error(`GitHub API returned ${response.status}`);
      }

      const issues = await response.json();
      const existingIssue = issues.find((issue) => (
        !isIgnoredSignupIssue(issue) && issueMentionsUsername(issue, username)
      ));

      if (existingIssue) {
        return existingIssue;
      }

      const linkHeader = response.headers.get("Link") ?? "";
      const nextLink = linkHeader.match(/<([^>]+)>;\s*rel="next"/);
      apiUrl = nextLink?.[1] ?? "";
    }

    return null;
  };

  form.addEventListener("submit", async (event) => {
    event.preventDefault();

    const username = input.value.trim().replace(/^@+/, "");
    const validUsername = /^[A-Za-z0-9](?:[A-Za-z0-9-]{0,37}[A-Za-z0-9])?$/.test(username);

    if (!validUsername) {
      input.setCustomValidity("请输入有效的 GitHub 用户名");
      input.reportValidity();
      return;
    }

    input.setCustomValidity("");
    submitButton?.setAttribute("disabled", "disabled");
    setStatus("正在检查是否已经报名...");

    const params = new URLSearchParams({
      template: "signup.yml",
      title: `[报名] ${username}`,
      github_username: username,
    });
    const signupUrl = `https://github.com/gevico/qemu-camp-tutorial/issues/new?${params.toString()}`;

    try {
      const existingIssue = await findExistingSignup(username);

      if (existingIssue) {
        setStatus(`GitHub 用户名 ${username} 已经提交过报名，请不要重复提交。`, "error", existingIssue.html_url);
        return;
      }

      setStatus("未发现重复报名，正在跳转到 GitHub...");
      window.location.href = signupUrl;
    } catch (error) {
      console.error(error);
      setStatus("暂时无法检查重复报名，正在跳转到 GitHub...");
      window.location.href = signupUrl;
    } finally {
      submitButton?.removeAttribute("disabled");
    }
  });
})();
</script>
