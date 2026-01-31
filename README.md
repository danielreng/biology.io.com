<!doctype html>
<html lang="en" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Biology Quest</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="/_sdk/element_sdk.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800&amp;display=swap" rel="stylesheet">
  <style>
    body {
      box-sizing: border-box;
    }
    
    * {
      font-family: 'Nunito', sans-serif;
    }
    
    .floating-cell {
      position: absolute;
      animation: float 8s ease-in-out infinite;
      opacity: 0.6;
      pointer-events: none;
    }
    
    @keyframes float {
      0%, 100% { transform: translateY(0) rotate(0deg); }
      25% { transform: translateY(-20px) rotate(5deg); }
      50% { transform: translateY(-10px) rotate(-3deg); }
      75% { transform: translateY(-25px) rotate(2deg); }
    }
    
    @keyframes celebrateLeaf {
      0% { transform: translateY(100%) rotate(0deg); opacity: 1; }
      100% { transform: translateY(-100%) rotate(360deg); opacity: 0; }
    }
    
    .celebrate-leaf {
      position: fixed;
      animation: celebrateLeaf 3s ease-out forwards;
      pointer-events: none;
      z-index: 100;
    }
    
    @keyframes pulse {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.05); }
    }
    
    .pulse-animation {
      animation: pulse 2s ease-in-out infinite;
    }
    
    @keyframes shimmer {
      0% { background-position: -200% center; }
      100% { background-position: 200% center; }
    }
    
    .badge-shimmer {
      background: linear-gradient(90deg, transparent, rgba(255,255,255,0.4), transparent);
      background-size: 200% 100%;
      animation: shimmer 2s infinite;
    }
    
    .progress-bar {
      transition: width 0.5s ease-out;
    }
    
    .option-btn {
      transition: all 0.2s ease;
    }
    
    .option-btn:hover {
      transform: translateY(-2px);
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
 </head>
 <body class="h-full m-0 overflow-auto">
  <div id="app-wrapper" class="w-full h-full relative overflow-hidden" style="background: linear-gradient(135deg, #e8f5e9 0%, #b2dfdb 50%, #e0f7fa 100%);"><!-- Floating Background Cells -->
   <div id="floating-cells" class="absolute inset-0 overflow-hidden pointer-events-none"></div><!-- Main Content -->
   <div class="relative z-10 w-full min-h-full flex flex-col items-center justify-center p-4"><!-- Start Screen -->
    <div id="start-screen" class="w-full max-w-lg text-center">
     <div class="rounded-3xl p-8 shadow-2xl" style="background: rgba(255,255,255,0.95);">
      <div class="text-6xl mb-4">
       🧬🔬🌱
      </div>
      <h1 id="title-text" class="text-3xl font-extrabold mb-3" style="color: #1b5e20;">Biology Quest</h1>
      <p id="welcome-text" class="text-lg mb-6" style="color: #2e7d32;">Test your knowledge of cells, plants, and animals!</p>
      <div class="mb-6">
       <p class="font-semibold mb-3" style="color: #388e3c;">Choose Your Level:</p>
       <div class="flex flex-col gap-3"><button onclick="startQuiz('easy')" class="option-btn w-full py-3 px-6 rounded-xl font-bold text-white shadow-lg" style="background: linear-gradient(135deg, #66bb6a, #43a047);"> 🌱 Beginner Explorer </button> <button onclick="startQuiz('medium')" class="option-btn w-full py-3 px-6 rounded-xl font-bold text-white shadow-lg" style="background: linear-gradient(135deg, #42a5f5, #1e88e5);"> 🌿 Science Adventurer </button> <button onclick="startQuiz('hard')" class="option-btn w-full py-3 px-6 rounded-xl font-bold text-white shadow-lg" style="background: linear-gradient(135deg, #ab47bc, #8e24aa);"> 🌳 Biology Master </button>
       </div>
      </div>
      <div class="text-sm" style="color: #689f38;"><span>🦠 Cells</span> • <span>🌱 Plants</span> • <span>🧬 DNA</span> • <span>🧫 Lab</span>
      </div>
     </div>
    </div><!-- Quiz Screen -->
    <div id="quiz-screen" class="w-full max-w-lg hidden">
     <div class="rounded-3xl p-6 shadow-2xl" style="background: rgba(255,255,255,0.95);"><!-- Progress Section -->
      <div class="mb-6">
       <div class="flex justify-between items-center mb-2"><span id="progress-plant" class="text-3xl">🌱</span> <span id="question-counter" class="font-bold" style="color: #2e7d32;">Question 1/5</span> <span id="score-display" class="font-bold" style="color: #1565c0;">Score: 0</span>
       </div>
       <div class="w-full h-3 rounded-full overflow-hidden" style="background: #c8e6c9;">
        <div id="progress-bar" class="progress-bar h-full rounded-full" style="width: 0%; background: linear-gradient(90deg, #66bb6a, #26a69a);"></div>
       </div>
      </div><!-- Question Section -->
      <div class="mb-6">
       <div id="question-emoji" class="text-5xl text-center mb-4">
        🦠
       </div>
       <h2 id="question-text" class="text-xl font-bold text-center mb-6" style="color: #1b5e20;"></h2>
       <div id="options-container" class="flex flex-col gap-3"></div>
      </div><!-- Feedback Section -->
      <div id="feedback-section" class="hidden text-center p-4 rounded-xl mb-4">
       <p id="feedback-text" class="font-bold text-lg"></p>
       <p id="feedback-explanation" class="text-sm mt-2"></p>
      </div><!-- Next Button --> <button id="next-btn" onclick="nextQuestion()" class="hidden w-full py-3 px-6 rounded-xl font-bold text-white shadow-lg option-btn" style="background: linear-gradient(135deg, #26a69a, #00897b);"> Next Question → </button>
     </div>
    </div><!-- Results Screen -->
    <div id="results-screen" class="w-full max-w-lg hidden">
     <div class="rounded-3xl p-8 shadow-2xl text-center" style="background: rgba(255,255,255,0.95);"><!-- Badge -->
      <div class="relative inline-block mb-6">
       <div class="w-32 h-32 rounded-full flex items-center justify-center pulse-animation" style="background: linear-gradient(135deg, #ffd54f, #ffb300); box-shadow: 0 8px 32px rgba(255, 179, 0, 0.4);">
        <div class="badge-shimmer absolute inset-0 rounded-full"></div><span class="text-5xl relative z-10">🏆</span>
       </div>
      </div>
      <h2 id="completion-text" class="text-2xl font-extrabold mb-2" style="color: #1b5e20;">Congratulations!</h2>
      <p id="result-title" class="text-xl font-bold mb-4" style="color: #2e7d32;">Junior Biologist</p>
      <div class="text-4xl mb-4">
       🔬🧪🧬
      </div>
      <div class="rounded-xl p-4 mb-6" style="background: linear-gradient(135deg, #e8f5e9, #b2dfdb);">
       <p class="text-3xl font-extrabold" style="color: #1b5e20;" id="final-score">5/5</p>
       <p class="text-sm" style="color: #388e3c;">Correct Answers</p>
      </div>
      <div id="achievement-text" class="text-sm mb-6" style="color: #689f38;"></div><button onclick="restartQuiz()" class="w-full py-3 px-6 rounded-xl font-bold text-white shadow-lg option-btn" style="background: linear-gradient(135deg, #66bb6a, #43a047);"> 🔄 Play Again </button>
     </div>
    </div>
   </div>
  </div>
  <script>
    const defaultConfig = {
      quiz_title: "Biology Quest",
      welcome_message: "Test your knowledge of cells, plants, and animals!",
      completion_message: "Congratulations!",
      primary_color: "#1b5e20",
      secondary_color: "#2e7d32",
      accent_color: "#26a69a",
      text_color: "#1b5e20",
      background_color: "#e8f5e9"
    };
    
    let config = { ...defaultConfig };
    
    const questions = {
      easy: [
        {
          emoji: "🦠",
          question: "What is the smallest unit of life?",
          options: ["Cell", "Atom", "Molecule", "Tissue"],
          correct: 0,
          explanation: "Cells are the basic building blocks of all living things! 🦠"
        },
        {
          emoji: "🌱",
          question: "What do plants need to make their own food?",
          options: ["Sunlight", "Meat", "Milk", "Candy"],
          correct: 0,
          explanation: "Plants use sunlight in a process called photosynthesis! ☀️🌱"
        },
        {
          emoji: "🧬",
          question: "What gives you traits like eye color from your parents?",
          options: ["DNA", "Food", "Water", "Exercise"],
          correct: 0,
          explanation: "DNA is like a recipe book that makes you unique! 🧬"
        },
        {
          emoji: "🦋",
          question: "What type of animal has six legs?",
          options: ["Insect", "Fish", "Bird", "Mammal"],
          correct: 0,
          explanation: "Insects like butterflies and ants all have six legs! 🐜"
        },
        {
          emoji: "🧫",
          question: "Where do scientists grow bacteria to study them?",
          options: ["Petri dish", "Flower pot", "Fish tank", "Bird cage"],
          correct: 0,
          explanation: "Petri dishes help scientists observe tiny organisms! 🧫"
        }
      ],
      medium: [
        {
          emoji: "🦠",
          question: "What part of the cell controls its activities?",
          options: ["Nucleus", "Cell wall", "Cytoplasm", "Membrane"],
          correct: 0,
          explanation: "The nucleus is like the cell's brain! 🧠"
        },
        {
          emoji: "🌱",
          question: "What gas do plants release during photosynthesis?",
          options: ["Oxygen", "Carbon dioxide", "Nitrogen", "Helium"],
          correct: 0,
          explanation: "Plants give us oxygen to breathe - thank a plant today! 🌿"
        },
        {
          emoji: "🧬",
          question: "What shape is a DNA molecule?",
          options: ["Double helix", "Square", "Triangle", "Circle"],
          correct: 0,
          explanation: "DNA looks like a twisted ladder called a double helix! 🧬"
        },
        {
          emoji: "🐸",
          question: "Frogs and salamanders belong to which animal group?",
          options: ["Amphibians", "Reptiles", "Mammals", "Fish"],
          correct: 0,
          explanation: "Amphibians can live both in water and on land! 🐸"
        },
        {
          emoji: "🧫",
          question: "What organelle gives plant cells their green color?",
          options: ["Chloroplast", "Mitochondria", "Nucleus", "Ribosome"],
          correct: 0,
          explanation: "Chloroplasts contain chlorophyll, which is green! 🌿"
        }
      ],
      hard: [
        {
          emoji: "🦠",
          question: "What is the powerhouse of the cell called?",
          options: ["Mitochondria", "Ribosome", "Golgi body", "Vacuole"],
          correct: 0,
          explanation: "Mitochondria produce energy (ATP) for the cell! ⚡"
        },
        {
          emoji: "🌱",
          question: "What are the tiny openings on leaves that allow gas exchange?",
          options: ["Stomata", "Roots", "Petals", "Stems"],
          correct: 0,
          explanation: "Stomata open and close to control water and gas! 🍃"
        },
        {
          emoji: "🧬",
          question: "Which bases pair together in DNA: Adenine with __?",
          options: ["Thymine", "Guanine", "Cytosine", "Uracil"],
          correct: 0,
          explanation: "A pairs with T, and C pairs with G in DNA! 🧬"
        },
        {
          emoji: "🦎",
          question: "Which classification level comes after Kingdom?",
          options: ["Phylum", "Class", "Order", "Family"],
          correct: 0,
          explanation: "The order is: Kingdom, Phylum, Class, Order, Family, Genus, Species! 📚"
        },
        {
          emoji: "🧫",
          question: "What process do cells use to divide and create two identical copies?",
          options: ["Mitosis", "Meiosis", "Osmosis", "Diffusion"],
          correct: 0,
          explanation: "Mitosis creates two identical daughter cells! 🦠🦠"
        }
      ]
    };
    
    let currentLevel = 'easy';
    let currentQuestionIndex = 0;
    let score = 0;
    let currentQuestions = [];
    let answered = false;
    
    function createFloatingCells() {
      const container = document.getElementById('floating-cells');
      container.innerHTML = '';
      const cells = ['🦠', '🔬', '🧬', '🧫', '🌱'];
      
      for (let i = 0; i < 12; i++) {
        const cell = document.createElement('div');
        cell.className = 'floating-cell';
        cell.textContent = cells[Math.floor(Math.random() * cells.length)];
        cell.style.left = Math.random() * 100 + '%';
        cell.style.top = Math.random() * 100 + '%';
        cell.style.fontSize = (Math.random() * 20 + 20) + 'px';
        cell.style.animationDelay = Math.random() * 5 + 's';
        cell.style.animationDuration = (Math.random() * 4 + 6) + 's';
        container.appendChild(cell);
      }
    }
    
    function startQuiz(level) {
      currentLevel = level;
      currentQuestionIndex = 0;
      score = 0;
      currentQuestions = [...questions[level]];
      shuffleArray(currentQuestions);
      
      document.getElementById('start-screen').classList.add('hidden');
      document.getElementById('quiz-screen').classList.remove('hidden');
      document.getElementById('results-screen').classList.add('hidden');
      
      showQuestion();
    }
    
    function shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    }
    
    function showQuestion() {
      answered = false;
      const question = currentQuestions[currentQuestionIndex];
      
      document.getElementById('question-emoji').textContent = question.emoji;
      document.getElementById('question-text').textContent = question.question;
      document.getElementById('question-counter').textContent = `Question ${currentQuestionIndex + 1}/${currentQuestions.length}`;
      document.getElementById('score-display').textContent = `Score: ${score}`;
      
      const progress = ((currentQuestionIndex) / currentQuestions.length) * 100;
      document.getElementById('progress-bar').style.width = progress + '%';
      
      updateProgressPlant();
      
      const optionsContainer = document.getElementById('options-container');
      optionsContainer.innerHTML = '';
      
      const shuffledOptions = question.options.map((opt, idx) => ({ text: opt, originalIndex: idx }));
      shuffleArray(shuffledOptions);
      
      shuffledOptions.forEach((option, idx) => {
        const btn = document.createElement('button');
        btn.className = 'option-btn w-full py-3 px-5 rounded-xl font-semibold text-left shadow-md';
        btn.style.background = '#ffffff';
        btn.style.border = '2px solid #a5d6a7';
        btn.style.color = '#1b5e20';
        btn.textContent = option.text;
        btn.onclick = () => selectAnswer(option.originalIndex, btn);
        optionsContainer.appendChild(btn);
      });
      
      document.getElementById('feedback-section').classList.add('hidden');
      document.getElementById('next-btn').classList.add('hidden');
    }
    
    function selectAnswer(selectedIndex, btn) {
      if (answered) return;
      answered = true;
      
      const question = currentQuestions[currentQuestionIndex];
      const isCorrect = selectedIndex === question.correct;
      
      const allBtns = document.querySelectorAll('#options-container button');
      allBtns.forEach(b => {
        b.style.opacity = '0.6';
        b.style.pointerEvents = 'none';
      });
      
      if (isCorrect) {
        score++;
        btn.style.background = 'linear-gradient(135deg, #66bb6a, #43a047)';
        btn.style.color = '#ffffff';
        btn.style.border = '2px solid #2e7d32';
        btn.style.opacity = '1';
        
        document.getElementById('feedback-section').style.background = 'linear-gradient(135deg, #c8e6c9, #a5d6a7)';
        document.getElementById('feedback-text').textContent = '🎉 Correct! Great job!';
        document.getElementById('feedback-text').style.color = '#1b5e20';
      } else {
        btn.style.background = 'linear-gradient(135deg, #ef5350, #e53935)';
        btn.style.color = '#ffffff';
        btn.style.border = '2px solid #c62828';
        btn.style.opacity = '1';
        
        allBtns.forEach(b => {
          if (b.textContent === question.options[question.correct]) {
            b.style.background = 'linear-gradient(135deg, #66bb6a, #43a047)';
            b.style.color = '#ffffff';
            b.style.border = '2px solid #2e7d32';
            b.style.opacity = '1';
          }
        });
        
        document.getElementById('feedback-section').style.background = 'linear-gradient(135deg, #ffcdd2, #ef9a9a)';
        document.getElementById('feedback-text').textContent = '❌ Not quite!';
        document.getElementById('feedback-text').style.color = '#c62828';
      }
      
      document.getElementById('feedback-explanation').textContent = question.explanation;
      document.getElementById('feedback-explanation').style.color = '#424242';
      document.getElementById('feedback-section').classList.remove('hidden');
      
      document.getElementById('score-display').textContent = `Score: ${score}`;
      updateProgressPlant();
      
      document.getElementById('next-btn').classList.remove('hidden');
      document.getElementById('next-btn').textContent = currentQuestionIndex === currentQuestions.length - 1 ? 'See Results 🏆' : 'Next Question →';
    }
    
    function updateProgressPlant() {
      const ratio = score / currentQuestions.length;
      let plant = '🌱';
      if (ratio >= 0.6) plant = '🌿';
      if (ratio >= 0.8) plant = '🌳';
      document.getElementById('progress-plant').textContent = plant;
    }
    
    function nextQuestion() {
      currentQuestionIndex++;
      
      if (currentQuestionIndex >= currentQuestions.length) {
        showResults();
      } else {
        showQuestion();
      }
    }
    
    function showResults() {
      document.getElementById('quiz-screen').classList.add('hidden');
      document.getElementById('results-screen').classList.remove('hidden');
      
      const ratio = score / currentQuestions.length;
      let title = 'Junior Biologist';
      let achievement = '';
      
      if (ratio === 1) {
        title = '🌟 Master Biologist 🌟';
        achievement = 'Perfect score! You\'re a biology superstar! 🎉';
      } else if (ratio >= 0.8) {
        title = '🥇 Expert Biologist';
        achievement = 'Amazing work! You really know your biology! 🌟';
      } else if (ratio >= 0.6) {
        title = '🥈 Skilled Biologist';
        achievement = 'Great job! Keep exploring the world of biology! 🔬';
      } else if (ratio >= 0.4) {
        title = '🥉 Junior Biologist';
        achievement = 'Good effort! Practice makes perfect! 📚';
      } else {
        title = '🔬 Biology Explorer';
        achievement = 'Keep learning! Every scientist starts somewhere! 🌱';
      }
      
      document.getElementById('result-title').textContent = title;
      document.getElementById('final-score').textContent = `${score}/${currentQuestions.length}`;
      document.getElementById('achievement-text').textContent = achievement;
      
      celebrateWithLeaves();
    }
    
    function celebrateWithLeaves() {
      const leaves = ['🍃', '🌿', '🌱', '🍀', '🌸'];
      
      for (let i = 0; i < 20; i++) {
        setTimeout(() => {
          const leaf = document.createElement('div');
          leaf.className = 'celebrate-leaf';
          leaf.textContent = leaves[Math.floor(Math.random() * leaves.length)];
          leaf.style.left = Math.random() * 100 + '%';
          leaf.style.bottom = '-50px';
          leaf.style.fontSize = (Math.random() * 20 + 20) + 'px';
          leaf.style.animationDuration = (Math.random() * 2 + 2) + 's';
          document.body.appendChild(leaf);
          
          setTimeout(() => leaf.remove(), 3000);
        }, i * 150);
      }
    }
    
    function restartQuiz() {
      document.getElementById('results-screen').classList.add('hidden');
      document.getElementById('start-screen').classList.remove('hidden');
    }
    
    async function onConfigChange(newConfig) {
      document.getElementById('title-text').textContent = newConfig.quiz_title || defaultConfig.quiz_title;
      document.getElementById('welcome-text').textContent = newConfig.welcome_message || defaultConfig.welcome_message;
      document.getElementById('completion-text').textContent = newConfig.completion_message || defaultConfig.completion_message;
    }
    
    function mapToCapabilities(cfg) {
      return {
        recolorables: [],
        borderables: [],
        fontEditable: undefined,
        fontSizeable: undefined
      };
    }
    
    function mapToEditPanelValues(cfg) {
      return new Map([
        ["quiz_title", cfg.quiz_title || defaultConfig.quiz_title],
        ["welcome_message", cfg.welcome_message || defaultConfig.welcome_message],
        ["completion_message", cfg.completion_message || defaultConfig.completion_message]
      ]);
    }
    
    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig,
        onConfigChange,
        mapToCapabilities,
        mapToEditPanelValues
      });
      config = window.elementSdk.config || defaultConfig;
    }
    
    createFloatingCells();
    onConfigChange(config);
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9c687ed861d4b195',t:'MTc2OTg1NTAyNi4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
