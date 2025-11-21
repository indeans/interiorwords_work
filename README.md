<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>인테리어 시공 용어 디지털 북</title>
    <!-- Tailwind CSS CDN 로드 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* 폰트 설정 및 배경 스타일링 */
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;500;700&display=swap');
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #f4f7f9; /* 부드러운 배경색 */
        }
        .book-container {
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
            transition: transform 0.3s ease;
        }
        .term-card {
            border-left: 5px solid;
            transition: all 0.2s ease;
        }
        .official { border-color: #3b82f6; /* Blue */ }
        .slang { border-color: #f59e0b; /* Amber */ }
        .category-tag {
            padding: 2px 8px;
            border-radius: 9999px;
            font-size: 0.75rem;
            font-weight: 500;
        }
        .official-tag { background-color: #dbeafe; color: #1e40af; }
        .slang-tag { background-color: #fef3c7; color: #b45309; }
        
        /* 아코디언 내용 숨기기 */
        .content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.3s ease-out;
            padding-left: 1rem;
            padding-right: 1rem;
        }
        .expanded .content {
            max-height: 500px; /* 불필요한 확장 제거 후 높이 재조정 */
            transition: max-height 0.5s ease-in;
        }
    </style>
</head>
<body class="p-4 sm:p-8">

    <div id="app" class="max-w-4xl mx-auto">

        <!-- 헤더 및 타이틀 -->
        <header class="text-center mb-8 p-4 bg-white rounded-xl shadow-md">
            <h1 class="text-3xl sm:text-4xl font-extrabold text-gray-800 tracking-tight">
                📐 인테리어 시공 용어 📚
            </h1>
            <p class="text-lg text-gray-500 mt-2">
                학생들을 위한 알기 쉬운 디지털 용어집 (현장 용어 & 은어 포함)
            </p>
        </header>

        <!-- 검색창 -->
        <div class="mb-6">
            <input type="text" id="search-input" placeholder="궁금한 용어를 검색해 보세요 (예: 야리끼리, 마감)"
                   class="w-full p-4 border-2 border-indigo-200 rounded-xl focus:ring-4 focus:ring-indigo-300 focus:border-indigo-500 text-gray-700 shadow-inner transition duration-150">
        </div>

        <!-- 용어 목록 컨테이너 -->
        <div id="glossary-list" class="space-y-4">
            <!-- 용어 카드는 JavaScript로 여기에 렌더링됩니다. -->
        </div>

        <!-- 검색 결과 없음 메시지 -->
        <div id="no-results" class="hidden text-center p-8 bg-white rounded-xl shadow-md mt-6">
            <p class="text-xl font-medium text-gray-600">
                🔍 검색 결과가 없습니다. 다른 키워드로 검색해 보세요.
            </p>
        </div>
    </div>

    <script>
        // LLM 관련 상수와 함수 (API_URL, apiKey, fetchWithRetry, callGeminiAPI)는 모두 제거되었습니다.
        
        // 용어 데이터 (JSON 형태)
        const termsData = [
            // --- 시공 용어 (Official Terms) ---
            { 
                term: "먹매김 (먹)", 
                category: "공정/기술", 
                type: "official", 
                definition: "건축 현장에서 기준선이나 위치를 표시하기 위해 먹물(먹통)을 사용하여 선을 긋는 작업이에요. '도면의 선을 현장에 옮겨 그린다'고 생각하면 돼요.",
                example: "벽을 세우기 전에 바닥에 정확하게 '먹'을 놓아야 해."
            },
            { 
                term: "마감", 
                category: "공정/기술", 
                type: "official", 
                definition: "시공의 마지막 단계로, 눈에 보이는 표면을 최종적으로 완성하는 작업이에요. 도배, 타일, 페인트칠 등이 '마감'에 해당됩니다.",
                example: "이제 '마감'만 남았으니 다음 주면 공사가 끝날 것 같아."
            },
            { 
                term: "양중 (揚重)", 
                category: "자재 운반", 
                type: "official", 
                definition: "무거운 자재(벽돌, 석고보드, 시멘트 등)를 트럭에서 현장이나 위층으로 들어 올리거나 운반하는 모든 작업이에요.",
                example: "석고보드가 너무 많아서 오늘은 '양중' 작업에 시간이 많이 걸릴 거야."
            },
            { 
                term: "퍼티 (Putty)", 
                category: "재료/마감", 
                type: "official", 
                definition: "벽이나 천장 표면에 생긴 작은 구멍, 흠집, 이음새 등을 메워 평평하고 매끄럽게 만드는 데 사용하는 반죽 형태의 재료입니다. 도장(페인트칠) 전에 필수적인 과정이에요.",
                example: "페인트칠을 깔끔하게 하려면 '퍼티' 작업을 여러 번 해야 해."
            },
            { 
                term: "레벨기", 
                category: "장비/도구", 
                type: "official", 
                definition: "레이저나 물방울을 이용해 작업할 면의 정확한 수평이나 수직을 측정하는 장비예요. 가구 설치나 타일 시공 시 매우 중요합니다.",
                example: "'레벨기'로 수평을 맞춘 뒤에 벽걸이 TV를 설치해야 삐뚤어지지 않아."
            },
            { 
                term: "견출", 
                category: "공정/기술", 
                type: "official", 
                definition: "콘크리트 벽면이나 천장의 거친 면을 끌이나 사포 등으로 곱게 다듬어 평평하고 깔끔하게 만드는 미장 작업의 일종입니다.",
                example: "노출 콘크리트 인테리어는 '견출' 작업이 정말 중요해. 마감이 전체 분위기를 좌우하거든."
            },
            { 
                term: "보양 (保養)", 
                category: "공정/안전", 
                type: "official", 
                definition: "시공 중인 공간이나 이미 시공된 부분이 손상되거나 오염되지 않도록 비닐, 박스 등으로 덮어 보호하는 작업이에요. '덮개로 보호한다'는 뜻이에요.",
                example: "바닥 타일이 손상되지 않게 박스로 철저히 '보양'해야 해."
            },
            { 
                term: "입회", 
                category: "현장 관리", 
                type: "official", 
                definition: "공정 진행 중 중요한 시점에 발주자나 감리자가 현장에 직접 참석하여 시공을 확인하고 승인하는 일이에요. '참여하여 지켜본다'는 의미입니다.",
                example: "배관 공사 후 다음 공정으로 넘어가기 전에 꼭 감리팀의 '입회'를 받아야 해요."
            },
            
            // --- 현장 은어 (Slang/Jargon) ---
            { 
                term: "야리끼리", 
                category: "현장 문화", 
                type: "slang", 
                definition: "정해진 작업량을 채우면 시간이 남았더라도 퇴근하는 방식이에요. '일당으로 하루 치 할당량을 끝낸다'는 뜻이 강합니다.",
                example: "오늘은 날씨가 더우니 오전에 '야리끼리'하고 쉬자."
            },
            { 
                term: "십장 (오야지)", 
                category: "인력/직책", 
                type: "slang", 
                definition: "현장에서 해당 공종(일의 종류)을 책임지고 작업자들을 관리하는 '작업 반장'을 낮춰 부르는 말이에요. (순화하여 '반장님'이라고 부르는 것이 좋습니다.)",
                example: "'십장'님이 오셔야 오늘 작업 지시를 받을 수 있어."
            },
            { 
                term: "함바 (함바집)", 
                category: "현장 문화", 
                type: "slang", 
                definition: "공사 현장 안에 설치된 임시 식당이나, 공사 현장에 식사를 배달해주는 업체를 뜻해요. 일본어에서 유래된 말입니다.",
                example: "점심시간이다! 오늘은 '함바'에서 김치찌개가 나온대."
            },
            { 
                term: "곰방", 
                category: "자재 운반", 
                type: "slang", 
                definition: "엘리베이터가 없거나 사용이 불가능할 때, 무거운 자재를 사람의 힘으로 직접 들거나 지고 계단을 오르내리며 나르는 일이에요. 고된 노동을 의미합니다.",
                example: "이번 현장은 5층까지 '곰방' 작업이 많아서 힘들어."
            },
            { 
                term: "데모도", 
                category: "인력/직책", 
                type: "slang", 
                definition: "숙련된 기술자('오야지' 또는 '기공') 옆에서 자재 정리, 청소, 보조 작업 등 단순한 일을 돕는 '보조 작업자'를 뜻해요. (일본어에서 유래)",
                example: "일 배우는 '데모도' 한 명만 더 붙여주시면 작업 속도가 훨씬 빨라질 거예요.",
                
            },
            { 
                term: "다데 (다데기)", 
                category: "방향/치수", 
                type: "slang", 
                definition: "'세로', '수직(Vertical)'을 뜻하는 일본어 잔재입니다. '다데 맞추다'는 수직을 맞춘다는 뜻이에요.",
                example: "'다데'가 안 맞아서 벽이 기울어 보이니, 수직을 다시 잡아줘."
            },
            { 
                term: "가네 (가네다)", 
                category: "방향/치수", 
                type: "slang", 
                definition: "'직각(90도)' 또는 '수평'을 뜻하는 일본어 잔재입니다. '가네 맞추다'는 직각을 정확히 맞춘다는 뜻이에요.",
                example: "코너 부분이 삐뚤어지지 않게 '가네'를 정확하게 잡고 타일을 붙여야 해."
            },
            { 
                term: "덴조", 
                category: "구조/천장", 
                type: "slang", 
                definition: "천장(Ceiling)을 뜻하는 일본식 은어예요. 현장에서는 '천장 공사' 자체를 의미하기도 하며, 주로 목공 작업에서 쓰입니다.",
                example: "천장 배선 작업 때문에 오늘은 '덴조' 안으로 들어가서 작업해야 해."
            },
            { 
                term: "나라시", 
                category: "공정/기술", 
                type: "slang", 
                definition: "땅이나 바닥면을 평평하게 고르는 작업을 뜻하는 일본식 은어예요. '평탄화 작업'이라고 순화하는 것이 좋아요.",
                example: "몰탈(시멘트)을 붓고 '나라시'를 잘해야 바닥이 울퉁불퉁하지 않아."
            },
            { 
                term: "하스리", 
                category: "공정/철거", 
                type: "slang", 
                definition: "콘크리트나 벽체 등을 깨거나 부수어 일부를 깎아내는 파괴/철거 작업을 뜻해요. 주로 망치나 드릴 같은 장비를 사용합니다.",
                example: "벽에 구멍을 내서 창문을 키우려면 시끄러운 '하스리' 작업이 필요해."
            },
            { 
                term: "슈미", 
                category: "마감/디테일", 
                type: "slang", 
                definition: "'줄눈' 또는 '틈'을 메우는 마감 작업을 뜻하는 일본식 은어예요. 특히 타일이나 목재 사이의 틈을 실리콘 등으로 채울 때 많이 쓰여요.",
                example: "타일 틈새 '슈미'를 실리콘으로 깨끗하게 마무리해주세요."
            }
        ];

        const glossaryList = document.getElementById('glossary-list');
        const searchInput = document.getElementById('search-input');
        const noResults = document.getElementById('no-results');

        // 함수: 용어 카드를 HTML로 생성
        function createTermCard(item) {
            const card = document.createElement('div');
            // 타입에 따른 스타일 적용
            const typeClass = item.type === 'official' ? 'official' : 'slang';
            const tagClass = item.type === 'official' ? 'official-tag' : 'slang-tag';
            const typeLabel = item.type === 'official' ? '표준 용어' : '현장 은어';
            
            // 모든 추가 기능(검색 링크, LLM 버튼) 관련 로직을 제거했습니다.

            card.className = `term-card ${typeClass} bg-white rounded-xl shadow-lg p-0 cursor-pointer overflow-hidden`;
            card.setAttribute('data-term', item.term);
            card.onclick = () => card.classList.toggle('expanded');

            card.innerHTML = `
                <!-- 헤더 (클릭 가능한 부분) -->
                <div class="header p-4 flex justify-between items-center hover:bg-gray-50 transition duration-150">
                    <div class="flex items-center">
                        <span class="text-xl font-bold text-gray-800 mr-3">${item.term}</span>
                        <span class="category-tag bg-indigo-100 text-indigo-700">${item.category}</span>
                    </div>
                    <div class="flex items-center">
                        <span class="category-tag ${tagClass} mr-3">${typeLabel}</span>
                        <svg class="w-5 h-5 text-gray-500 transform transition-transform duration-300" 
                             xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                        </svg>
                    </div>
                </div>

                <!-- 내용 (숨겨진 부분) -->
                <div class="content p-4 pt-0">
                    <div class="border-t border-gray-100 mt-2 pt-4 space-y-3">
                        <p class="text-gray-700 leading-relaxed">
                            <span class="font-semibold text-blue-600">📌 정의:</span> ${item.definition}
                        </p>
                        <p class="text-gray-600 italic bg-gray-50 p-3 rounded-lg border border-gray-100">
                            <span class="font-semibold text-red-600">💬 예시:</span> ${item.example}
                        </p>
                    </div>
                </div>
            `;
            return card;
        }

        // 함수: 용어 목록을 렌더링 (검색 필터링 포함)
        function renderGlossary(filterText = '') {
            glossaryList.innerHTML = '';
            const lowerCaseFilter = filterText.toLowerCase().trim();
            let hasResults = false;

            termsData.forEach(item => {
                const termLower = item.term.toLowerCase();
                const definitionLower = item.definition.toLowerCase();
                const categoryLower = item.category.toLowerCase();

                // 검색 필터링: 용어, 정의, 카테고리에 포함되는지 확인
                if (lowerCaseFilter === '' || 
                    termLower.includes(lowerCaseFilter) ||
                    definitionLower.includes(lowerCaseFilter) ||
                    categoryLower.includes(lowerCaseFilter)) {
                    
                    const card = createTermCard(item);
                    glossaryList.appendChild(card);
                    hasResults = true;
                }
            });

            // 검색 결과 없음 메시지 표시/숨기기
            if (!hasResults) {
                noResults.classList.remove('hidden');
            } else {
                noResults.classList.add('hidden');
            }
        }

        // 이벤트 리스너: 검색창 입력
        searchInput.addEventListener('input', (e) => {
            renderGlossary(e.target.value);
        });

        // 초기 렌더링
        window.onload = () => {
            renderGlossary();
        };

    </script>
</body>
</html>
