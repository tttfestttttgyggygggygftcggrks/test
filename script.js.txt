const QUESTIONS = [
  {
    q: "ベンチプレスで主に鍛えられる筋肉はどれ？",
    c: ["<ruby>大胸筋<rt>だいきょうきん</rt></ruby>", "<ruby>上腕二頭筋<rt>じょうわんにとうきん</rt></ruby>", "<ruby>僧帽筋<rt>そうぼうきん</rt></ruby>", "<ruby>広背筋<rt>こうはいきん</rt></ruby>"],
    a: 0
  },
  {
    q: "スクワットで最も大きな刺激を受ける筋肉は？",
    c: ["<ruby>ハムストリング<rt>はむすとりんぐ</rt></ruby>", "<ruby>大腿四頭筋<rt>だいたいしとうきん</rt></ruby>", "<ruby>臀筋<rt>でんきん</rt></ruby>", "<ruby>前腕筋<rt>ぜんわんきん</rt></ruby>"],
    a: 1
  },
  {
    q: "プランクが特に鍛える部位は？",
    c: ["<ruby>体幹<rt>たいかん</rt></ruby>（コア）", "<ruby>腓腹筋<rt>ひふくきん</rt></ruby>", "<ruby>上腕三頭筋<rt>じょうわんさんとうきん</rt></ruby>", "<ruby>三角筋<rt>さんかくきん</rt></ruby>"],
    a: 0
  },
  {
    q: "デッドリフトで主に鍛えられるのは？",
    c: ["<ruby>広背筋<rt>こうはいきん</rt></ruby>・<ruby>脊柱起立筋<rt>せきちゅうきりつきん</rt></ruby>", "<ruby>腹直筋<rt>ふくちょくきん</rt></ruby>", "<ruby>三角筋<rt>さんかくきん</rt></ruby>", "<ruby>上腕二頭筋<rt>じょうわんにとうきん</rt></ruby>"],
    a: 0
  },
  {
    q: "懸垂（チンニング）で特に強化されるのは？",
    c: ["<ruby>広背筋<rt>こうはいきん</rt></ruby>", "<ruby>大腿四頭筋<rt>だいたいしとうきん</rt></ruby>", "<ruby>腹筋<rt>ふっきん</rt></ruby>", "<ruby>僧帽筋<rt>そうぼうきん</rt></ruby>"],
    a: 0
  }
];

let idx = 0;
let score = 0;
let results = [];
let selectedIndex = null;

const questionEl = document.getElementById("question");
const choicesEl = document.getElementById("choices");
const nextBtn = document.getElementById("next");
const resultEl = document.getElementById("result");
const scoreEl = document.getElementById("score");
const detailEl = document.getElementById("detail");
const quizArea = document.getElementById("quiz");
const introEl = document.getElementById("intro");
const bar = document.getElementById("bar");
const qcount = document.getElementById("qcount");
const startBtn = document.getElementById("start");

startBtn.onclick = () => {
  introEl.classList.add("hidden");
  quizArea.classList.remove("hidden");
  showQuestion();
};

function updateProgress() {
  bar.style.width = Math.round(((idx + 1) / QUESTIONS.length) * 100) + '%';
  qcount.textContent = `${idx + 1} / ${QUESTIONS.length}`;
}

function showQuestion() {
  const q = QUESTIONS[idx];
  questionEl.innerHTML = q.q;
  choicesEl.innerHTML = "";
  selectedIndex = null;

  q.c.forEach((choice, i) => {
    const li = document.createElement("li");
    li.innerHTML = choice;
    li.onclick = () => selectChoice(i, li);
    choicesEl.appendChild(li);
  });

  updateProgress();
}

function selectChoice(i, li) {
  // ✅ 選び直しできるようにする
  Array.from(choicesEl.children).forEach(el => el.classList.remove("selected"));
  li.classList.add("selected");
  selectedIndex = i;
}

nextBtn.onclick = () => {
  const q = QUESTIONS[idx];

  if (selectedIndex === null) {
    alert("選択肢を選んでください！");
    return;
  }

  let correct = selectedIndex === q.a;
  if (correct) score++;

  results.push({
    question: q.q,
    userAnswer: q.c[selectedIndex],
    correctAnswer: q.c[q.a],
    correct: correct
  });

  idx++;
  if (idx < QUESTIONS.length) {
    showQuestion();
  } else {
    finish();
  }
};

function finish() {
  quizArea.classList.add("hidden");
  resultEl.classList.remove("hidden");
  scoreEl.textContent = `あなたのスコア: ${score} / ${QUESTIONS.length}`;

  let html = "";
  if (score === QUESTIONS.length) {
    const facts = [
      "筋肉は48時間以上の休養でより成長する！",
      "スクワットは“キング・オブ・エクササイズ”と呼ばれる。",
      "プランクは1分でも体幹強化に効果的。",
      "デッドリフトは全身運動として非常に効率的。",
      "懸垂は広背筋を鍛える最強の自重トレーニング。"
    ];
    const fact = facts[Math.floor(Math.random() * facts.length)];
    html += `🎉 全問正解！すごい！<br><br>豆知識: ${fact}<br><br>`;
  }

  const wrongs = results.filter(r => !r.correct);
  if (wrongs.length > 0) {
    html += "<h3>間違えた問題と正解:</h3><ul>";
    wrongs.forEach(r => {
      html += `<li><b>Q:</b> ${r.question}<br>
               あなたの答え: ${r.userAnswer}<br>
               正解: ${r.correctAnswer}</li><br>`;
    });
    html += "</ul>";
  }

  detailEl.innerHTML = html;
}
