<template>
  <div id="app">
    <header class="header">
      <h1>🎯 三角洲行动装备抽奖器</h1>
      <p>随机抽取你的战斗装备，开始你的三角洲行动！</p>
    </header>

    <div class="lottery-container">
      <!-- 抽奖类别选择 -->
      <div class="category-selector">
        <h3>选择抽奖类别：</h3>
        <div class="category-buttons">
          <button 
            v-for="category in categories" 
            :key="category.name"
            :class="{ active: selectedCategory === category.name }"
            @click="selectCategory(category.name)"
          >
            {{ category.icon }} {{ category.name }}
          </button>
        </div>
      </div>

      <!-- 抽奖区域 -->
      <div class="lottery-area">
        <div class="lottery-wheel" :class="{ spinning: isSpinning }">
          <div class="wheel-content">
            <div v-if="!isSpinning && !lotteryResult" class="wheel-placeholder">
              <div class="wheel-icon">🎲</div>
              <p>点击开始抽奖</p>
            </div>
            <div v-else-if="isSpinning" class="wheel-spinning">
              <div class="spinning-text">抽奖中...</div>
            </div>
            <div v-else-if="lotteryResult" class="wheel-result">
              <div class="result-icon">{{ lotteryResult.icon }}</div>
              <div class="result-name">{{ lotteryResult.name }}</div>
              <div class="result-description">{{ lotteryResult.description }}</div>
            </div>
          </div>
        </div>

        <button 
          class="lottery-button" 
          @click="startLottery"
          :disabled="isSpinning || !selectedCategory"
        >
          {{ isSpinning ? '抽奖中...' : '开始抽奖' }}
        </button>
      </div>

      <!-- 抽奖历史 -->
      <div v-if="lotteryHistory.length > 0" class="lottery-history">
        <h3>抽奖历史：</h3>
        <div class="history-list">
          <div 
            v-for="(item, index) in lotteryHistory" 
            :key="index"
            class="history-item"
          >
            <span class="history-category">{{ item.category }}</span>
            <span class="history-result">{{ item.icon }} {{ item.name }}</span>
          </div>
        </div>
        <button @click="clearHistory" class="clear-button">清空历史</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      selectedCategory: null,
      isSpinning: false,
      lotteryResult: null,
      lotteryHistory: [],
      categories: {
        '人物': {
          name: '人物',
          icon: '⛑️',
          items: [
            { name: '盾构', icon: '⛑️', description: '' },
            { name: '乌鲁鲁', icon: '⛑️', description: '' },
            { name: '露娜', icon: '⛑️', description: '' },
            { name: '小麦', icon: '⛑️', description: '' },
            { name: '红狼', icon: '⛑️', description: '' },
            { name: '张姐', icon: '⛑️', description: '' },
            { name: '威风的龙', icon: '⛑️', description: '' },
            { name: '鼠鼠', icon: '⛑️', description: '' },
            { name: '无名', icon: '⛑️', description: '' },
            { name: '老黑', icon: '⛑️', description: '' },
          ]
        },
        '武器': {
          name: '武器',
          icon: '🔫',
          items: [
            { name: 'AKM突击步枪', icon: '🔫', description: '5.56mm口径，精准稳定，射速中等' },
            { name: 'SCAR-H战斗步枪', icon: '🔫', description: '5.45mm口径，火力强劲，后坐力大' },
            { name: 'KC17突击步枪', icon: '🔫', description: '5.56mm口径，精准稳定，射速中等' },
            { name: 'AUG突击步枪', icon: '🔫', description: '9mm口径，射速快，适合近距离' },
            { name: 'MP7冲锋枪', icon: '💥', description: '5.56mm口径，火力压制，弹药充足' },
            { name: 'AK-12突击步枪', icon: '🔫', description: '.50口径，威力巨大，精度高' },
            { name: 'M4A1突击步枪', icon: '🎯', description: '.50口径，反器材狙击步枪' },
            { name: 'M249轻机枪', icon: '💥', description: '5.56mm口径，火力压制，弹药充足' },
            { name: 'PTR-32突击步枪', icon: '🔫', description: '5.7mm口径，独特造型，高射速' },
            { name: 'M14射手步枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: 'M7战斗步枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: 'M250通用机枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: 'PKM通用机枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: 'K437突击步枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: '腾龙突击步枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: 'SR-3M紧凑突击步枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: 'AS Val突击步枪', icon: '🔫', description: '7.62mm口径，模块化设计' },
            { name: 'ASh-12战斗步枪', icon: '🔫', description: '12.7mm口径，大口径战斗步枪' },
            { name: 'K416突击步枪', icon: '🔫', description: '7.62mm口径，模块化设计' },

          ]
        },
        '头盔': {
          name: '头盔',
          icon: '⛑️',
          items: [
            { name: '军用头盔L1', icon: '⛑️', description: '基础防护，重量轻，机动性好' },
            { name: '军用头盔L2', icon: '⛑️', description: '增强防护，重量适中，性价比高' },
            { name: '军用头盔L3', icon: '⛑️', description: '高级防护，重量较重，防护全面' },
            { name: '军用头盔L4', icon: '⛑️', description: '顶级防护，重量重，最高防护等级' },
            { name: '军用头盔L5', icon: '⛑️', description: '传奇防护，重量最重，极致防护' },
            { name: '夜视头盔', icon: '⛑️', description: '集成夜视功能，夜间作战专用' },
          ]
        },
        '护甲': {
          name: '护甲',
          icon: '🛡️',
          items: [
            { name: '战术背心L1', icon: '🛡️', description: '基础护甲，轻便灵活，防护一般' },
            { name: '战术背心L2', icon: '🛡️', description: '标准护甲，平衡防护与机动性' },
            { name: '战术背心L3', icon: '🛡️', description: '重型护甲，高防护，机动性下降' },
            { name: '战术背心L4', icon: '🛡️', description: '顶级护甲，极高防护，重量重' },
            { name: '战术背心L5', icon: '🛡️', description: '传奇护甲，最高防护，重量最重' },
          ]
        },
        '胸挂': {
          name: '胸挂',
          icon: '🎒',
          items: [
            { name: '基础胸挂(1)', icon: '🎒', description: '4个弹夹槽，轻便实用' },
            { name: '标准胸挂(2)', icon: '🎒', description: '6个弹夹槽，平衡容量与重量' },
            { name: '重型胸挂(3)', icon: '🎒', description: '8个弹夹槽，弹药充足，重量重' },
            { name: '特种胸挂(4)', icon: '🎒', description: '6个弹夹槽+2个装备槽，多功能' },
            { name: '突击胸挂(5)', icon: '🎒', description: '10个弹夹槽，火力持续，重量最重' }
          ]
        },
        '背包': {
          name: '背包',
          icon: '🎒',
          items: [
            { name: '小型背包(1)', icon: '🎒', description: '容量小，重量轻，适合快速移动' },
            { name: '中型背包(2)', icon: '🎒', description: '容量适中，平衡存储与机动性' },
            { name: '大型背包(3)', icon: '🎒', description: '容量大，重量重，适合持久战' },
            { name: '军用背包(4)', icon: '🎒', description: '专业设计，容量大，耐用性强' },
            { name: '战术背包(5)', icon: '🎒', description: '模块化设计，可扩展性强' }
          ]
        },
        '子弹等级': {
          name: '子弹等级',
          icon: '🔸',
          items: [
            { name: '子弹等级1', icon: '🔸', description: '基础弹药，伤害一般，穿透力弱' },
            { name: '子弹等级2', icon: '🔸', description: '标准弹药，伤害提升，穿透力中等' },
            { name: '子弹等级3', icon: '🔸', description: '高级弹药，伤害较高，穿透力强' },
            { name: '子弹等级4', icon: '🔸', description: '顶级弹药，伤害很高，穿透力极强' },
            { name: '子弹等级5', icon: '🔸', description: '传奇弹药，伤害最高，穿透力最强' }
          ]
        },
        '地图': {
          name: '地图',
          icon: '🗺️',
          items: [
            { name: '大坝', icon: '🗺️', description: '开阔沙漠地形，适合远程作战' },
            { name: '巴克什', icon: '🗺️', description: '复杂城市环境，适合近距离战斗' },
            { name: '监狱', icon: '🗺️', description: '高低起伏地形，战术多变' },
            { name: '航天', icon: '🗺️', description: '植被茂密，适合隐蔽作战' }
          ]
        },
        '等级': {
          name: '等级',
          icon: '⭐',
          items: [
            { name: '机密', icon: '⭐', description: '高级权限等级，获取机密信息' },
            { name: '绝密', icon: '⭐⭐', description: '最高权限等级，获取绝密信息' },
          ]
        }
      }
    }
  },
  methods: {
    selectCategory(categoryName) {
      this.selectedCategory = categoryName;
      this.lotteryResult = null;
    },
    startLottery() {
      if (!this.selectedCategory || this.isSpinning) return;
      
      this.isSpinning = true;
      this.lotteryResult = null;
      
      // 模拟抽奖过程
      setTimeout(() => {
        const items = this.categories[this.selectedCategory].items;
        const randomIndex = Math.floor(Math.random() * items.length);
        const result = items[randomIndex];
        
        this.lotteryResult = result;
        this.isSpinning = false;
        
        // 添加到历史记录
        this.lotteryHistory.unshift({
          category: this.selectedCategory,
          ...result
        });
        
        // 限制历史记录数量
        if (this.lotteryHistory.length > 10) {
          this.lotteryHistory = this.lotteryHistory.slice(0, 10);
        }
      }, 2000);
    },
    clearHistory() {
      this.lotteryHistory = [];
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
  min-height: 100vh;
  font-family: 'Microsoft YaHei', Arial, sans-serif;
}

#app {
  min-height: 100vh;
  padding: 20px;
  color: white;
}

.header {
  text-align: center;
  margin-bottom: 40px;
}

.header h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
}

.header p {
  font-size: 1.1rem;
  opacity: 0.9;
}

.lottery-container {
  max-width: 800px;
  margin: 0 auto;
}

.category-selector {
  background: rgba(255,255,255,0.1);
  padding: 30px;
  border-radius: 15px;
  margin-bottom: 30px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255,255,255,0.2);
}

.category-selector h3 {
  margin-bottom: 20px;
  font-size: 1.3rem;
}

.category-buttons {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 15px;
}

.category-buttons button {
  padding: 15px 20px;
  border: none;
  border-radius: 10px;
  background: rgba(255,255,255,0.2);
  color: white;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid transparent;
}

.category-buttons button:hover {
  background: rgba(255,255,255,0.3);
  transform: translateY(-2px);
}

.category-buttons button.active {
  background: rgba(255,255,255,0.4);
  border-color: #ffd700;
  box-shadow: 0 0 20px rgba(255,215,0,0.3);
}

.lottery-area {
  text-align: center;
  margin-bottom: 40px;
}

.lottery-wheel {
  width: 300px;
  height: 300px;
  margin: 0 auto 30px;
  border-radius: 50%;
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1, #96ceb4, #feca57);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
  transition: transform 0.3s ease;
  border: 5px solid rgba(255,255,255,0.3);
}

.lottery-wheel.spinning {
  animation: spin 2s linear infinite;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.wheel-content {
  width: 250px;
  height: 250px;
  border-radius: 50%;
  background: rgba(255,255,255,0.9);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #333;
  text-align: center;
}

.wheel-placeholder .wheel-icon {
  font-size: 4rem;
  margin-bottom: 10px;
}

.wheel-placeholder p {
  font-size: 1.1rem;
  font-weight: bold;
}

.wheel-spinning .spinning-text {
  font-size: 1.5rem;
  font-weight: bold;
  color: #666;
  animation: pulse 1s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

.wheel-result .result-icon {
  font-size: 3rem;
  margin-bottom: 10px;
}

.wheel-result .result-name {
  font-size: 1.3rem;
  font-weight: bold;
  margin-bottom: 5px;
  color: #2c3e50;
}

.wheel-result .result-description {
  font-size: 0.9rem;
  color: #666;
  padding: 0 10px;
}

.lottery-button {
  padding: 15px 40px;
  font-size: 1.2rem;
  background: linear-gradient(45deg, #ff6b6b, #ee5a24);
  color: white;
  border: none;
  border-radius: 25px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 5px 15px rgba(0,0,0,0.2);
}

.lottery-button:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(0,0,0,0.3);
}

.lottery-button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.lottery-history {
  background: rgba(255,255,255,0.1);
  padding: 30px;
  border-radius: 15px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255,255,255,0.2);
}

.lottery-history h3 {
  margin-bottom: 20px;
  font-size: 1.3rem;
}

.history-list {
  margin-bottom: 20px;
}

.history-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 15px;
  margin-bottom: 10px;
  background: rgba(255,255,255,0.1);
  border-radius: 8px;
  border-left: 4px solid #ffd700;
}

.history-category {
  font-weight: bold;
  color: #ffd700;
}

.history-result {
  font-size: 1.1rem;
}

.clear-button {
  padding: 10px 20px;
  background: rgba(255,255,255,0.2);
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.clear-button:hover {
  background: rgba(255,255,255,0.3);
}

@media (max-width: 600px) {
  .header h1 {
    font-size: 2rem;
  }
  
  .lottery-wheel {
    width: 250px;
    height: 250px;
  }
  
  .wheel-content {
    width: 200px;
    height: 200px;
  }
  
  .category-buttons {
    grid-template-columns: 1fr;
  }
}
</style>
