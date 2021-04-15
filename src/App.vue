<template>
  <div id="app">
    <div class='calculator' @keyup.delete='clickKey($event)'>
      <!-- 입력한 연산식 -->
      <div class='result cal' style='grid-area: result'>
        {{txtCal}} {{tempCal}}<br/>
      </div>
      <!-- 입력한 숫자 -->
      <div class='result' style='grid-area: command'>
        {{number}}<br/>
      </div>
      <!-- 기타 기능 -->
      <button class='btn accent' style='grid-area: ac' @click='init'>AC</button>
      <button class='plma' style='grid-area: plus-minus' @click='setSign'>±</button>
      <button class='btn accent' style='grid-area: del' @click='delNum'>DEL</button>
      <button style='grid-area: equal' @click='setResult'>=</button>
      <button style="grid-area: dot" @click="addDot">.</button>
      <!-- /,*,-,+ 연산 -->
      <button
        v-for='cal in cals' :key='cal.id'
        @click='appendCal(cal.op)'
        :style="'grid-area: ' + cal.id"
        class='btn cal'>
        {{cal.key}}
      </button>
      <!-- 숫자 -->
      <button
        v-for='number in 10' :key='number + 10'
        @click='appendNum((number - 1) + "")'
        :style="'grid-area: number-' + (number-1)"
        class='num'>
        {{number - 1}}
      </button>
    </div>
    <div class='right'>© 2021. yeonju.jin. all rights reserved</div>
  </div>
</template>
<script>
export default {
  name: 'App',
  data () {
    return {
      number: '0', // 사용자가 입력한 숫자
      cals : [
        { key: '÷', op : '/', id : 'divide' },
        { key: 'x', op : '*', id : 'multiply' },
        { key: '-', op : '-', id : 'subtract' },
        { key: '+', op : '+', id : 'add' }
      ], // 연산의 종류
      txtCal: '', // 연산식
      tempCal: '', // 일시적인 연산자
      sign: '', // 부호
      clickType: 1 // 0: 연산자 입력중, 1: 숫자 입력중, 2. '=' 이후
    }
  },
  watch: {
    number () {
      if (this.number === '' || this.number === '-') this.number = '0'
    }
  },
  mounted () {
    window.addEventListener('keypress', this.clickKey)
  },
  methods: {
    appendCal (code) {
      if (this.clickType) { // 숫자입력 -> 연산자 입력
        this.txtCal += ' ' + this.tempCal + ' ' + this.number
        this.number = eval(this.txtCal) + ''
        this.tempCal = code

        this.clickType = 0 // 연산자 입력중
      } else {
        this.tempCal = code
      }
    },
    appendNum (number) {
      if (this.clickType === 1) { // 숫자 입력중
        if (this.number === '0') this.number = number
        else this.number += number
      } else { // 연산자 입력 -> 숫자입력
        this.number = number
        this.clickType = 1
      }
    },
    init () {
      this.tempCal = ''
      this.txtCal = ''
      this.number = '0'
      this.clickType = 1
    },
    setSign () {
      this.number = ( this.number * -1 ) + ''
      this.clickType = 1
    },
    setResult () { // '=' 클릭
      const temp = this.txtCal + this.tempCal + this.number
      this.init()
      this.clickType = 2
      this.number = eval(temp) + ''
    },
    addDot () {
      // 입력한 숫자에 '.'이 없는 경우에만
      if (this.number.indexOf('.') < 0) {
        this.number += '.'
        this.clickType = 1
      }
    },
    delNum () {
      this.number = this.number.slice(0, -1)
    },
    clickKey (event) {
      const keyCode = event.keyCode
      if (keyCode >= 48 && keyCode) { // 숫자 입력
        this.appendNum(event.key)
      } else if (keyCode === 43 || keyCode === 45 ||
        keyCode === 47 || keyCode ===42
      ) { // 연산자 입력
        this.appendCal(event.key)
      } else if (keyCode === 61 || keyCode === 13) { // '=', enter
        this.setResult()
      } else if (keyCode === 46) { // dot
        this.addDot()
      }
    }
  }
}
</script>