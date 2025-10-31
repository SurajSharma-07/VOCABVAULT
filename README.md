<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VocabVault - Language Learning App</title>
    
    <!-- 1. Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- 2. Google Font (Inter) -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    
    <!-- 3. Lucide Icons -->
    <script src="https://unpkg.com/lucide-react@latest/dist/umd/lucide.min.js"></script>

    <!-- 4. Custom Styles (for flashcard flip and other minor tweaks) -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f9fafb; /* Changed to Tailwind bg-gray-50 */
        }
        
        /* Custom message box for feedback (no changes) */
        #message-box {
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            padding: 12px 24px;
            border-radius: 8px;
            font-weight: 500;
            opacity: 0;
            transition: opacity 0.3s ease, transform 0.3s ease;
            z-index: 1000;
        }
        #message-box.show {
            opacity: 1;
            transform: translateX(-50%) translateY(-20px);
        }

        /* Flashcard flip animation */
        .flashcard {
            perspective: 1000px; /* Gives the 3D effect */
        }
        .flashcard-inner {
            position: relative;
            width: 100%;
            height: 100%;
            transition: transform 0.6s;
            transform-style: preserve-3d;
        }
        .flashcard.is-flipped .flashcard-inner {
            transform: rotateY(180deg);
        }
        .flashcard-front,
        .flashcard-back {
            position: absolute;
            width: 100%;
            height: 100%;
            -webkit-backface-visibility: hidden; /* Safari */
            backface-visibility: hidden;
            border-radius: 0.5rem; /* 8px */
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 1.5rem; /* 24px */
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05); /* Softer shadow */
        }
        .flashcard-back {
            transform: rotateY(180deg);
        }

        /* Loading Spinner (No longer used, but kept for potential future use) */
        #loading-spinner {
            display: none; 
        }

    </style>
</head>
<body class="bg-gray-50 text-gray-800">

    <!-- Container -->
    <div class="container mx-auto max-w-6xl p-4 md:p-8">

        <!-- Header -->
        <header class="mb-8 p-6 bg-gradient-to-r from-blue-600 to-indigo-700 text-white rounded-lg shadow-lg border border-blue-700">
            <div class="flex flex-col sm:flex-row justify-between items-center">
                <div>
                    <h1 class="text-3xl font-bold">VocabVault</h1>
                    <p class="text-md text-indigo-100 opacity-90"> learning playground </p>
                </div>
                <!-- User ID Display Removed -->
            </div>
        </header>

        <!-- Main Content (Grid) -->
        <main class="grid grid-cols-1 md:grid-cols-2 gap-8">

            <!-- Left Column: Add Word Form -->
            <section class="bg-white p-6 rounded-xl shadow-lg border border-gray-200">
                <h2 class="text-2xl font-semibold mb-5">Add a New Word</h2>
                <form id="add-word-form" class="space-y-4">
                    <div class="relative">
                        <label for="word" class="block text-sm font-medium text-gray-700">Word</label>
                        <div class="absolute inset-y-0 left-0 pl-3 pt-7 flex items-center pointer-events-none">
                            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-gray-400"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.72"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg>
                        </div>
                        <input type="text" id="word" name="word" required class="mt-1 block w-full pl-10 px-3 py-2 bg-white border border-gray-300 rounded-md text-sm shadow-sm placeholder-gray-400 focus:outline-none focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div class="relative">
                        <label for="translation" class="block text-sm font-medium text-gray-700">Translation</label>
                        <div class="absolute inset-y-0 left-0 pl-3 pt-7 flex items-center pointer-events-none">
                             <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-gray-400"><path d="m5 8 6 6"></path><path d="m4 14 6-6 2-3"></path><path d="M2 5h12"></path><path d="M7 2h1"></path><path d="m22 22-5-10-5 10"></path><path d="M14 18h6"></path></svg>
                        </div>
                        <input type="text" id="translation" name="translation" required class="mt-1 block w-full pl-10 px-3 py-2 bg-white border border-gray-300 rounded-md text-sm shadow-sm placeholder-gray-400 focus:outline-none focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div class="relative">
                        <label for="language" class="block text-sm font-medium text-gray-700">Language</label>
                        <div class="absolute inset-y-0 left-0 pl-3 pt-7 flex items-center pointer-events-none">
                            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-gray-400"><circle cx="12" cy="12" r="10"></circle><line x1="2" y1="12" x2="22" y2="12"></line><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"></path></svg>
                        </div>
                        <select id="language" name="language" required class="mt-1 block w-full pl-10 px-3 py-2 bg-white border border-gray-300 rounded-md text-sm shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500">
                            <option value="">Select a language...</option>
                            <option value="Spanish">Spanish</option>
                            <option value="French">French</option>
                            <option value="German">German</option>
                            <option value="Japanese">Japanese</option>
                            <option value="Mandarin">Mandarin</option>
                            <option value="Italian">Italian</option>
                            <option value="Other">Other</option>
                        </select>
                    </div>
                    <div class="relative">
                        <label for="context" class="block text-sm font-medium text-gray-700">Context / Sentence (Optional)</label>
                         <div class="absolute inset-y-0 left-0 pl-3 pt-7 flex items-center pointer-events-none">
                            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-gray-400"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path></svg>
                        </div>
                        <textarea id="context" name="context" rows="3" class="mt-1 block w-full pl-10 px-3 py-2 bg-white border border-gray-300 rounded-md text-sm shadow-sm placeholder-gray-400 focus:outline-none focus:ring-blue-500 focus:border-blue-500"></textarea>
                    </div>
                    <button type="submit" id="save-word-btn" class="w-full flex justify-center py-3 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 transition-all hover:shadow-lg">
                        Save Word
                    </button>
                </form>
            </section>

            <!-- Right Column: Review Section -->
            <section class="bg-white p-6 rounded-xl shadow-lg border border-gray-200">
                <div class="flex justify-between items-center mb-5">
                    <h2 class="text-2xl font-semibold">Review Your Words</h2>
                    <div class="text-sm font-medium text-gray-500 bg-gray-100 px-3 py-1 rounded-full">
                        Total: <span id="total-words-counter" class="font-bold text-blue-600">0</span>
                    </div>
                </div>

                <!-- View Toggles -->
                <div class="flex border-b border-gray-200 mb-4">
                    <button id="show-list-btn" class="flex-1 py-2 text-sm font-medium text-blue-600 border-b-2 border-blue-600">List View</button>
                    <button id="show-flashcard-btn" class="flex-1 py-2 text-sm font-medium text-gray-500 hover:text-gray-700">Flashcard View</button>
                </div>

                <!-- Loading Spinner (Hidden) -->
                <div class="flex justify-center my-4">
                     <div id="loading-spinner"></div>
                </div>

                <!-- List View -->
                <div id="list-view">
                    <div id="list-empty-msg" class="text-center text-gray-500 p-8 hidden">
                        <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" class="mx-auto text-gray-400 mb-4"><path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"></path><path d="M6.5 2H20v15H6.5A2.5 2.5 0 0 1 4 14.5V4.5A2.5 2.5 0 0 1 6.5 2z"></path></svg>
                        <h4 class="font-semibold text-lg text-gray-700">Your vault is empty!</h4>
                        <p class="text-sm">Add a new word using the form to start your learning journey.</p>
                    </div>
                    <ul id="vocab-list" class="space-y-3">
                        <!-- Words will be dynamically inserted here -->
                    </ul>
                </div>

                <!-- Flashcard View -->
                <div id="flashcard-view" class="hidden">
                    <div id="flashcard-empty-msg" class="text-center text-gray-500 p-8 hidden">
                        <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" class="mx-auto text-gray-400 mb-4"><path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"></path><path d="M6.5 2H20v15H6.5A2.5 2.5 0 0 1 4 14.5V4.5A2.5 2.5 0 0 1 6.5 2z"></path></svg>
                        <h4 class="font-semibold text-lg text-gray-700">No words to review</h4>
                        <p class="text-sm">Add a new word to start reviewing with flashcards.</p>
                    </div>
                    
                    <!-- Flashcard container -->
                    <div id="flashcard-container" class="relative">
                        <div id="flashcard" class="flashcard w-full h-64 rounded-lg cursor-pointer">
                            <div class="flashcard-inner">
                                <!-- Front of card -->
                                <div id="flashcard-front" class="flashcard-front bg-blue-100 text-blue-900 shadow-md border border-blue-200">
                                    <span class="text-xs text-blue-500 mb-2">Word</span>
                                    <p class="text-3xl font-bold">Konnichiwa</p>
                                </div>
                                <!-- Back of card -->
                                <div id="flashcard-back" class="flashcard-back bg-emerald-100 text-emerald-900 shadow-md border border-emerald-200">
                                    <span class="text-xs text-emerald-500 mb-2">Translation & Context</span>
                                    <p class="text-2xl font-semibold">Hello</p>
                                    <p class="text-sm mt-2 italic">"Konnichiwa, Tanaka-san."</p>
                                </div>
                            </div>
                        </div>
                        <p class="text-center text-gray-400 text-xs mt-2">Click card to flip</p>

                        <!-- Navigation -->
                        <div id="flashcard-nav" class="flex justify-between items-center mt-4">
                            <button id="prev-card-btn" class="p-2 rounded-full bg-gray-100 hover:bg-gray-200 text-gray-600">
                                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg>
                            </button>
                            <span id="flashcard-counter" class="text-sm font-medium text-gray-500">1 / 10</span>
                            <button id="next-card-btn" class="p-2 rounded-full bg-gray-100 hover:bg-gray-200 text-gray-600">
                                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"></polyline></svg>
                            </button>
                        </div>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <!-- Message Box (for non-alert feedback) -->
    <div id="message-box"></div>

    <!-- 5. App Logic (No Firebase) -->
    <script type="module">
        // --- GLOBAL VARIABLES ---
        let vocabList = []; // Local cache of vocabulary
        let currentFlashcardIndex = 0;

        // --- DOM ELEMENTS ---
        const addWordForm = document.getElementById('add-word-form');
        const saveWordBtn = document.getElementById('save-word-btn');
        const vocabListEl = document.getElementById('vocab-list');
        const listEmptyMsg = document.getElementById('list-empty-msg');
        const totalWordsCounter = document.getElementById('total-words-counter');
        
        // View containers and buttons
        const showListBtn = document.getElementById('show-list-btn');
        const showFlashcardBtn = document.getElementById('show-flashcard-btn');
        const listView = document.getElementById('list-view');
        const flashcardView = document.getElementById('flashcard-view');

        // Flashcard specific elements
        const flashcardContainer = document.getElementById('flashcard-container');
        const flashcard = document.getElementById('flashcard');
        const flashcardFront = document.getElementById('flashcard-front');
        const flashcardBack = document.getElementById('flashcard-back');
        const flashcardCounter = document.getElementById('flashcard-counter');
        const prevCardBtn = document.getElementById('prev-card-btn');
        const nextCardBtn = document.getElementById('next-card-btn');
        const flashcardNav = document.getElementById('flashcard-nav');
        const flashcardEmptyMsg = document.getElementById('flashcard-empty-msg');

        // Message box
        const messageBox = document.getElementById('message-box');

        // --- HELPER FUNCTIONS ---

        /**
         * Shows a feedback message
         * @param {string} message - The text to display
         * @param {string} type - 'success', 'error', or 'info'
         */
        function showMessage(message, type = 'error') {
            messageBox.textContent = message;
            
            // Reset classes
            messageBox.classList.remove('bg-red-500', 'bg-green-500', 'bg-blue-500', 'text-white');
            
            if (type === 'success') {
                messageBox.classList.add('bg-green-500', 'text-white');
            } else if (type === 'info') {
                messageBox.classList.add('bg-blue-500', 'text-white');
            } else {
                messageBox.classList.add('bg-red-500', 'text-white');
            }
            
            messageBox.classList.add('show');
            
            // Hide after 3 seconds
            setTimeout(() => {
                messageBox.classList.remove('show');
            }, 3000);
        }

        /**
         * Updates the total word count display
         */
        function updateTotalCounter() {
            totalWordsCounter.textContent = vocabList.length;
        }

        // --- DATA OPERATIONS (Local Array) ---

        /**
         * Handles the form submission to save a new word
         */
        function handleSaveWord(e) {
            e.preventDefault();

            const word = addWordForm.word.value.trim();
            const translation = addWordForm.translation.value.trim();
            const language = addWordForm.language.value;
            const context = addWordForm.context.value.trim();

            if (!word || !translation || !language) {
                showMessage("Please fill in Word, Translation, and Language.", "error");
                return;
            }

            saveWordBtn.disabled = true;
            saveWordBtn.textContent = "Saving...";

            try {
                const newWord = {
                    id: Date.now().toString(), // Simple unique ID
                    word: word,
                    translation: translation,
                    language: language,
                    context: context
                };

                // Add to the beginning of the array
                vocabList.unshift(newWord); 

                showMessage("Word saved!", "success");
                addWordForm.reset();

                // Update UI
                updateTotalCounter();
                renderListView();
                renderFlashcardView(); // Update flashcard view in case it's active

            } catch (error) {
                console.error("Error saving word locally:", error);
                showMessage("Failed to save word.", "error");
            } finally {
                saveWordBtn.disabled = false;
                saveWordBtn.textContent = "Save Word";
            }
        }

        /**
         * Deletes a word from the local array
         */
        function handleDeleteWord(wordId) {
            if (!wordId) {
                showMessage("Cannot delete. Invalid ID.", "error");
                return;
            }
            
            // Filter out the word to delete
            vocabList = vocabList.filter(word => word.id !== wordId);
            
            showMessage("Word deleted.", "info");

            // Update UI
            updateTotalCounter();
            renderListView();
            
            // Reset flashcard index if it goes out of bounds
            if (currentFlashcardIndex >= vocabList.length) {
                currentFlashcardIndex = 0;
            }
            renderFlashcardView();
        }

        // --- UI RENDERING ---

        /**
         * Renders the list of vocabulary words
         */
        function renderListView() {
            vocabListEl.innerHTML = ''; // Clear existing list

            if (vocabList.length === 0) {
                listEmptyMsg.style.display = 'block';
                return;
            }
            
            listEmptyMsg.style.display = 'none';

            vocabList.forEach(word => {
                const li = document.createElement('li');
                li.className = "p-4 bg-white border border-gray-200 rounded-lg flex justify-between items-start shadow-sm hover:shadow-lg hover:scale-[1.01] transition-all duration-200";
                
                li.innerHTML = `
                    <div class="flex-1 pr-4">
                        <div class="flex items-center">
                            <h4 class="text-lg font-semibold text-blue-700">${word.word}</h4>
                            <span class="ml-2 text-xs font-medium bg-gray-200 text-gray-600 px-2 py-0.5 rounded-full">${word.language}</span>
                        </div>
                        <p class="text-md text-gray-800">${word.translation}</p>
                        ${word.context ? `<p class="text-sm text-gray-500 italic mt-1">"${word.context}"</p>` : ''}
                    </div>
                    <button data-id="${word.id}" class="delete-btn flex-shrink-0 p-2 text-gray-400 hover:text-red-500 hover:bg-red-100 rounded-full">
                        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path><line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line></svg>
                    </button>
                `;
                
                vocabListEl.appendChild(li);
            });
            
            // Add event listeners to new delete buttons
            vocabListEl.querySelectorAll('.delete-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    handleDeleteWord(btn.dataset.id);
                });
            });
        }
        
        /**
         * Renders the current flashcard
         */
        function renderFlashcardView() {
            // Reset flipped state
            flashcard.classList.remove('is-flipped');
            
            if (vocabList.length === 0) {
                flashcardEmptyMsg.style.display = 'block';
                flashcardContainer.style.display = 'none';
                return;
            }

            flashcardEmptyMsg.style.display = 'none';
            flashcardContainer.style.display = 'block';

            // Ensure index is valid
            if (currentFlashcardIndex < 0 || currentFlashcardIndex >= vocabList.length) {
                currentFlashcardIndex = 0;
            }
            
            const word = vocabList[currentFlashcardIndex];

            // Populate front
            flashcardFront.innerHTML = `
                <span class="text-xs text-blue-600 mb-2">Word (${word.language})</span>
                <p class="text-3xl font-bold text-center">${word.word}</p>
            `;
            
            // Populate back
            flashcardBack.innerHTML = `
                <span class="text-xs text-emerald-600 mb-2">Translation & Context</span>
                <p class="text-2xl font-semibold text-center">${word.translation}</p>
                ${word.context ? `<p class="text-sm mt-2 italic text-center">"${word.context}"</p>` : ''}
            `;
            
            // Update counter
            flashcardCounter.textContent = `${currentFlashcardIndex + 1} / ${vocabList.length}`;
        }

        // --- EVENT LISTENERS ---

        /**
         * Switches to the list view
         */
        function showListView() {
            listView.style.display = 'block';
            flashcardView.style.display = 'none';
            showListBtn.classList.add('text-blue-600', 'border-blue-600');
            showListBtn.classList.remove('text-gray-500', 'hover:text-gray-700');
            showFlashcardBtn.classList.add('text-gray-500', 'hover:text-gray-700');
            showFlashcardBtn.classList.remove('text-blue-600', 'border-blue-600');
        }

        /**
         * Switches to the flashcard view
         */
        function showFlashcardView() {
            listView.style.display = 'none';
            flashcardView.style.display = 'block';
            showFlashcardBtn.classList.add('text-blue-600', 'border-blue-600');
            showFlashcardBtn.classList.remove('text-gray-500', 'hover:text-gray-700');
            showListBtn.classList.add('text-gray-500', 'hover:text-gray-700');
            showListBtn.classList.remove('text-blue-600', 'border-blue-600');
            
            // Re-render in case data has changed
            renderFlashcardView();
        }

        // --- INITIALIZE THE APP ---
        
        window.addEventListener('DOMContentLoaded', () => {
            // Form submission
            addWordForm.addEventListener('submit', handleSaveWord);
            
            // View switching
            showListBtn.addEventListener('click', showListView);
            showFlashcardBtn.addEventListener('click', showFlashcardView);

            // Flashcard interaction
            flashcard.addEventListener('click', () => {
                flashcard.classList.toggle('is-flipped');
            });
            
            nextCardBtn.addEventListener('click', () => {
                if (vocabList.length > 0) {
                    currentFlashcardIndex = (currentFlashcardIndex + 1) % vocabList.length;
                    renderFlashcardView();
                }
            });
            
            prevCardBtn.addEventListener('click', () => {
                if (vocabList.length > 0) {
                    currentFlashcardIndex = (currentFlashcardIndex - 1 + vocabList.length) % vocabList.length;
                    renderFlashcardView();
                }
            });
            
            // Set initial view and states
            showListView();
            renderListView();
            renderFlashcardView();
            updateTotalCounter();
            
            // Init Lucide icons
            if (window.lucide) {
                lucide.createIcons();
            }
        });

    </script>

</body>
</html>

