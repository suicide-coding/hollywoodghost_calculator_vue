<template>
  <div class="calculator-wrapper">
    <div class="calculator">
      <div class="calculator__header">
        <div class="calculator__topbar">
          <span class="calculator__more">more</span>
          <span class="calculator__clock">
            <svg width="18" height="18" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10" fill="none" stroke="#fff" stroke-width="2"/><path d="M12 7v5l3 3" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" fill="none"/></svg>
          </span>
        </div>
        <div class="calculator__display">
          <div class="calculator__expression">{{ expression }}</div>
          <div class="calculator__result">{{ displayValue }}</div>
        </div>
        <div class="calculator__dog">
          <!-- SVG мордочки собаки -->
          <svg viewBox="0 0 120 48" width="90" height="36">
            <ellipse cx="60" cy="44" rx="54" ry="18" fill="#fff"/>
            <path d="M30 18 Q20 0 40 8" fill="#fff" stroke="#F9A23B" stroke-width="3"/>
            <path d="M90 18 Q100 0 80 8" fill="#fff" stroke="#F9A23B" stroke-width="3"/>
            <ellipse cx="60" cy="38" rx="12" ry="6" fill="#F9A23B"/>
            <ellipse cx="48" cy="28" rx="8" ry="8" fill="#fff" stroke="#F9A23B" stroke-width="2"/>
            <ellipse cx="72" cy="28" rx="8" ry="8" fill="#fff" stroke="#F9A23B" stroke-width="2"/>
            <ellipse cx="54" cy="30" rx="2" ry="3" fill="#000"/>
            <ellipse cx="66" cy="30" rx="2" ry="3" fill="#000"/>
            <ellipse cx="60" cy="38" rx="2" ry="1.2" fill="#000"/>
            <path d="M58 36 Q60 40 62 36" stroke="#000" stroke-width="2" fill="none"/>
            <ellipse cx="60" cy="42" rx="3" ry="1.2" fill="#fff"/>
            <ellipse cx="56" cy="34" rx="1" ry="0.5" fill="#fff"/>
            <ellipse cx="64" cy="34" rx="1" ry="0.5" fill="#fff"/>
            <ellipse cx="60" cy="41" rx="1.2" ry="0.5" fill="#F97C3D"/>
          </svg>
        </div>
      </div>
      <div class="calculator__buttons">
        <button class="btn btn-func" @click="clearAll">C</button>
        <button class="btn btn-func" @click="percent">%</button>
        <button class="btn btn-func" @click="inputOperator('/')">÷</button>
        <button class="btn btn-func btn-del" @click="deleteLast">del</button>
        <button class="btn" @click="inputDigit('7')">7</button>
        <button class="btn" @click="inputDigit('8')">8</button>
        <button class="btn" @click="inputDigit('9')">9</button>
        <button class="btn btn-op" @click="inputOperator('*')">×</button>
        <button class="btn" @click="inputDigit('4')">4</button>
        <button class="btn" @click="inputDigit('5')">5</button>
        <button class="btn" @click="inputDigit('6')">6</button>
        <button class="btn btn-op" @click="inputOperator('-')">-</button>
        <button class="btn" @click="inputDigit('1')">1</button>
        <button class="btn" @click="inputDigit('2')">2</button>
        <button class="btn" @click="inputDigit('3')">3</button>
        <button class="btn btn-op" @click="inputOperator('+')">+</button>
        <button class="btn btn-zero" @click="inputDigit('0')">0</button>
        <button class="btn" @click="inputDot">.</button>
        <button class="btn btn-eq" @click="calculate">=</button>
      </div>
    </div>
    <div v-if="history.length" class="calculator-history">
      <div class="history-title">Recent actions</div>
      <ul>
        <li v-for="(item, idx) in history" :key="idx">
          <span class="history-exp">{{ item.exp }}</span>
          <span class="history-res">= {{ item.res }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const displayValue = ref('0')
const expression = ref('')
let waitingForOperand = ref(false)
const history = ref([])

function inputDigit(digit) {
  if (displayValue.value.length > 12) return
  if (waitingForOperand.value) {
    displayValue.value = digit
    waitingForOperand.value = false
  } else {
    displayValue.value = displayValue.value === '0' ? digit : displayValue.value + digit
  }
}

function inputDot() {
  if (waitingForOperand.value) {
    displayValue.value = '0.'
    waitingForOperand.value = false
    return
  }
  if (!displayValue.value.includes('.')) {
    displayValue.value += '.'
  }
}

function clearAll() {
  displayValue.value = '0'
  expression.value = ''
  waitingForOperand.value = false
}

function deleteLast() {
  if (waitingForOperand.value) return
  if (displayValue.value.length === 1) {
    displayValue.value = '0'
  } else {
    displayValue.value = displayValue.value.slice(0, -1)
  }
}

function inputOperator(op) {
  if (expression.value && waitingForOperand.value) {
    expression.value = expression.value.slice(0, -1) + op
    return
  }
  expression.value += displayValue.value + ' ' + op + ' '
  waitingForOperand.value = true
}

function percent() {
  displayValue.value = (parseFloat(displayValue.value) / 100).toString()
}

function calculate() {
  try {
    let exp = expression.value + displayValue.value
    if (!/^[-+*/.\d\s]+$/.test(exp)) return
    let result = eval(exp)
    displayValue.value = result.toString().slice(0, 14)
    if (expression.value) {
      history.value.unshift({ exp: exp.replace(/\*/g, '×').replace(/\//g, '÷'), res: displayValue.value })
      if (history.value.length > 5) history.value.pop()
    }
    expression.value = ''
    waitingForOperand.value = true
  } catch {
    displayValue.value = 'Error'
    expression.value = ''
    waitingForOperand.value = true
  }
}
</script>

<style scoped>
.calculator-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.calculator-history {
  margin-top: 12px;
  background: rgba(255,255,255,0.92);
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  padding: 10px 16px 8px 16px;
  width: 220px;
  font-size: 13px;
}
.history-title {
  font-weight: 700;
  color: #F9A23B;
  margin-bottom: 4px;
  font-size: 13px;
}
.calculator-history ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
.calculator-history li {
  display: flex;
  justify-content: space-between;
  margin-bottom: 2px;
}
.history-exp {
  color: #4B3950;
}
.history-res {
  color: #F57C00;
  font-weight: 600;
}
.calculator {
  width: 260px;
  background: #fff;
  border-radius: 28px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.12);
  overflow: hidden;
  font-family: 'Inter', Arial, sans-serif;
  display: flex;
  flex-direction: column;
}
.calculator__header {
  background: linear-gradient(135deg, #FFA63D 70%, #fff 100%);
  border-top-left-radius: 28px;
  border-top-right-radius: 28px;
  padding: 12px 14px 0 14px;
  position: relative;
  min-height: 120px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
}
.calculator__topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 4px;
}
.calculator__more {
  color: #fff;
  font-size: 12px;
  opacity: 0.8;
  font-weight: 600;
  letter-spacing: 1px;
}
.calculator__clock {
  display: flex;
  align-items: center;
  opacity: 0.8;
}
.calculator__display {
  text-align: right;
  color: #fff;
  margin-bottom: 4px;
  min-height: 36px;
}
.calculator__expression {
  font-size: 13px;
  opacity: 0.8;
  min-height: 16px;
}
.calculator__result {
  font-size: 24px;
  font-weight: bold;
  margin-top: 2px;
  min-height: 24px;
}
.calculator__dog {
  display: flex;
  justify-content: center;
  margin-top: -8px;
  margin-bottom: 0;
}
.calculator__buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px 6px;
  padding: 14px 14px 14px 14px;
  background: #fff;
  border-bottom-left-radius: 28px;
  border-bottom-right-radius: 28px;
}
.btn {
  background: #fff;
  border: none;
  border-radius: 12px;
  font-size: 18px;
  font-weight: 700;
  padding: 10px 0;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
  color: #222;
  outline: none;
  user-select: none;
}
.btn:active {
  background: #FFE0B2;
}
.btn-func {
  background: #FFE0B2;
  color: #F9A23B;
  font-weight: bold;
}
.btn-op {
  background: #FFD180;
  color: #F57C00;
  font-weight: bold;
}
.btn-eq {
  background: #F9A23B;
  color: #fff;
  grid-column: 3 / span 2;
  font-weight: bold;
  border-radius: 12px;
}
.btn-zero {
  grid-column: 1 / span 2;
}
.btn-del {
  grid-column: 4;
  grid-row: 1;
  background: #FFE0B2;
  color: #F9A23B;
  font-weight: bold;
}
</style> 