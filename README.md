click bot for enso


(async () => {
  function getRandomString(length = 6) {
    const chars = "abcdefghijklmnopqrstuvwxyz";
    let result = "";
    for (let i = 0; i < length; i++) {
      result += chars[Math.floor(Math.random() * chars.length)];
    }
    return result;
  }

  let B = 1547;         // 初始变量 B
  const TIMES = 5;      // 执行次数
  const delay = (ms) => new Promise(res => setTimeout(res, ms));

  function setNativeValue(el, value) {
    const lastValue = el.value;
    el.value = value;
    const tracker = el._valueTracker;
    if (tracker) tracker.setValue(lastValue);
    el.dispatchEvent(new Event("input", { bubbles: true }));
  }

  async function waitForSelector(selector, timeout = 3000) {
    const start = Date.now();
    while (Date.now() - start < timeout) {
      const el = document.querySelector(selector);
      if (el) return el;
      await delay(200);
    }
    return null;
  }

  // 🆕 点击“DeFi”图标
  const defiImg = [...document.querySelectorAll("img")].find(img =>
    img.alt?.toLowerCase() === "defi"
  );
  if (defiImg) {
    const parentClickable = defiImg.closest("a, div, button");
    if (parentClickable) {
      parentClickable.click();
      console.log("✅ 点击了 DeFi 图标，等待页面加载...");
      await delay(2000);
    } else {
      console.warn("⚠️ 找到了图标但没有可点击的父元素");
    }
  } else {
    console.warn("❌ 没有找到 DeFi 图标");
    return;
  }

  let isFirstRun = true;

  for (let i = 0; i < TIMES; i++) {
    const A = getRandomString();  // 生成随机字符串
    const fullName = `${A}${B}`;
    console.log(`🔁 第 ${i + 1} 次：创建 ${fullName}`);

    const dexBtn = [...document.querySelectorAll("button")].find(btn =>
      btn.textContent.trim().toLowerCase().includes("defi dex")
    );
    if (!dexBtn) {
      console.warn("❌ 没有找到 DeFi DEX 按钮");
      return;
    }
    dexBtn.click();
    await delay(isFirstRun ? 2000 : 1000); // ⏱ 首次等待较长，之后较短

    const nameInput = await waitForSelector("input#name");
    const subdomainInput = await waitForSelector("input[name='subdomain']");
    if (!nameInput || !subdomainInput) {
      console.warn("❌ 无法找到输入框");
      return;
    }
    setNativeValue(nameInput, fullName);
    setNativeValue(subdomainInput, fullName);

    const submitBtn = [...document.querySelectorAll("button")].find(btn =>
      btn.textContent.trim().toLowerCase() === "submit"
    );
    if (!submitBtn) {
      console.warn("❌ 没有找到 Submit 按钮");
      return;
    }
    submitBtn.click();
    console.log("✅ 已点击 Submit，等待部署结果...");
    await delay(isFirstRun ? 3000 : 1500); // ⏱ 首次等待3秒，之后1.5秒

    let success = false;
    for (let j = 0; j < 10; j++) { // 减少轮数加快整体流程
      if ([...document.querySelectorAll("*")].some(el => el.textContent.includes("DeFi DEX deployed successfully"))) {
        success = true;
        console.log("🎉 部署成功！");
        break;
      }
      await delay(500);
    }

    const closeBtn = [...document.querySelectorAll("button")].find(btn =>
      btn.textContent.toLowerCase().includes("close") || btn.textContent.includes("关闭")
    );
    if (closeBtn) {
      http://closeBtn.click();
      console.log("✅ 已点击 Close，等待1秒...");
      await delay(isFirstRun ? 3000 : 1000); // ⏱ 首次3秒，之后1秒
    } else {
      console.warn("⚠️ 没找到 Close 按钮");
    }

    B += 1;
    isFirstRun = false; // ⏲️ 之后循环全部执行较快版本
  }

  console.log("🏁 所有流程已完成！");
})();
