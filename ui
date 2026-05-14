import streamlit as st

st.set_page_config(page_title="3D Da Vinci Code UI", page_icon="🧩", layout="centered")

# ==========================================
# 1. 고급 3D 애니메이션 및 UI 스타일 CSS
# ==========================================
st.markdown("""
    <style>
    /* 화면 중앙 정렬 및 여백 조정 */
    .block-container { padding-top: 1rem; }
   
    /* 사이드바 UI 꾸미기 */
    [data-testid="stSidebar"] { background-color: #f8f9fa; }
   
    /* 타일 컨테이너 (3D 원근감) */
    .tile-container {
        display: flex;
        gap: 15px;
        justify-content: center;
        margin: 40px 0;
        perspective: 1000px;
    }

    /* 플립 애니메이션 래퍼 */
    .flip-wrapper {
        background-color: transparent;
        width: 80px;
        height: 120px;
        perspective: 1000px;
        cursor: pointer;
    }

    /* 회전하는 본체 */
    .dv-tile {
        position: relative;
        width: 100%;
        height: 100%;
        text-align: center;
        transition: transform 0.6s cubic-bezier(0.4, 0.2, 0.2, 1);
        transform-style: preserve-3d;
        /* 기본적으로 뒤로 살짝 눕힘 */
        transform: rotateX(15deg);
    }

    /* 마우스 호버 시 살짝 일어나는 효과 (입체감) */
    .flip-wrapper:hover .dv-tile {
        transform: rotateX(0deg) translateY(-10px);
    }

    /* 플립 클래스가 붙으면 180도 회전 (뒤집기) */
    .dv-tile.is-flipped {
        transform: rotateX(0deg) rotateY(180deg);
    }

    /* 타일 앞면, 뒷면 공통 속성 */
    .tile-face {
        position: absolute;
        width: 100%;
        height: 100%;
        -webkit-backface-visibility: hidden;
        backface-visibility: hidden;
        border-radius: 12px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 36px;
        font-weight: bold;
        font-family: monospace;
    }

    /* 타일 색상 디자인 */
    .face-black {
        background: linear-gradient(145deg, #333333, #000000);
        color: #ffffff;
        border: 1px solid #444;
        box-shadow: 0 4px 0 #222, 0 10px 15px rgba(0,0,0,0.4);
    }
    .face-white {
        background: linear-gradient(145deg, #ffffff, #e6e6e6);
        color: #111111;
        border: 1px solid #ccc;
        box-shadow: 0 4px 0 #bbb, 0 10px 15px rgba(0,0,0,0.2);
    }

    /* 물음표가 있는 뒷면 (Back - 가려진 상태) */
    .tile-back {
        transform: rotateY(0deg);
    }
    .tile-back::after {
        content: "?";
        font-size: 40px;
        color: rgba(128, 128, 128, 0.4);
    }

    /* 숫자가 있는 앞면 (Front - 공개된 상태) */
    .tile-front {
        transform: rotateY(180deg);
    }

    /* 선택된 타일 하이라이트 (네온 효과) */
    .is-targeted .dv-tile {
        box-shadow: 0 0 20px #00ffcc;
        transform: rotateX(0deg) translateY(-15px);
        outline: 3px solid #00ffcc;
        border-radius: 12px;
    }
    </style>
""", unsafe_allow_html=True)

# ==========================================
# 2. 사이드바 (게임 상태창 UI)
# ==========================================
with st.sidebar:
    st.header("📊 게임 현황")
    st.markdown("---")
    st.markdown("**현재 턴:** 🟢 플레이어 (Player)")
    st.markdown("---")
    st.subheader("남은 블록")
    col1, col2 = st.columns(2)
    col1.metric(label="⬛ 흑색", value="6개")
    col2.metric(label="⬜ 백색", value="4개")
    st.markdown("---")
    st.info("💡 **UI 팁:** 로직 담당자에게 'is_flipped' 와 'is_targeted' 클래스 조건을 데이터에 연동해달라고 하세요!")

# ==========================================
# 3. 더미 데이터 (UI 테스트용)
# ==========================================
# (색상, 숫자, 숨김여부, 타겟선택여부)
# 세 번째 타일을 '선택(Targeted)' 상태로, 네 번째 타일을 '뒤집힌(Flipped)' 상태로 강제 설정해 시각적 확인.
opponent_hand = [
    ('W', 2, True, False),
    ('B', 5, True, False),
    ('B', 7, True, True),   # 이 타일이 하이라이트 됩니다.
    ('W', 11, False, False) # 이 타일은 공개되어 뒤집힌 앞면을 보여줍니다.
]

my_hand = [
    ('B', 0, False, False),
    ('W', 4, False, False),
    ('B', 8, False, False),
    ('W', 10, False, False)
]

# ==========================================
# 4. 타일 렌더링 헬퍼 함수 (고급 애니메이션 적용)
# ==========================================
def render_advanced_hand(hand_data, label):
    st.markdown(f"<h3 style='text-align: center; color: #555;'>{label}</h3>", unsafe_allow_html=True)
   
    html_str = '<div class="tile-container">'
    for color, number, is_hidden, is_targeted in hand_data:
        color_class = "face-black" if color == 'B' else "face-white"
       
        # UI 제어용 클래스
        flip_class = "" if is_hidden else "is-flipped"
        target_class = "is-targeted" if is_targeted else ""
       
        # 구조: Wrapper > 본체(dv-tile) > 앞면(Front, 숫자) / 뒷면(Back, 물음표)
        html_str += f'''
            <div class="flip-wrapper {target_class}">
                <div class="dv-tile {flip_class}">
                    <div class="tile-face tile-back {color_class}"></div>
                    <div class="tile-face tile-front {color_class}">
                        <span>{number}</span>
                    </div>
                </div>
            </div>
        '''
    html_str += '</div>'
   
    st.markdown(html_str, unsafe_allow_html=True)

# ==========================================
# 5. 화면 레이아웃 및 렌더링
# ==========================================
st.title("🧩 Da Vinci Code")
st.caption("UI Frontend Demonstration")

# 상대방 패 출력
render_advanced_hand(opponent_hand, "🤖 상대방 패")

st.markdown("<div style='height: 30px;'></div>", unsafe_allow_html=True)

# 내 패 출력
render_advanced_hand(my_hand, "🧑 내 패")

st.markdown("---")

# 추리 입력 폼
st.subheader("🎯 타일 지목하기")
cols = st.columns([2, 2, 1])

with cols[0]:
    st.selectbox("지목할 상대 타일", ["1번째", "2번째", "3번째", "4번째"], index=2)
with cols[1]:
    st.number_input("예상 숫자", min_value=0, max_value=11, value=7)
with cols[2]:
    st.markdown("<div style='margin-top: 28px;'></div>", unsafe_allow_html=True) # 버튼 높이 맞춤
    st.button("추리 실행", type="primary", use_container_width=True)
