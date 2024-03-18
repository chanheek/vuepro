<template>
  <div class="container">
    <button class="btn" @click="startRecognition">음성 인식 시작</button>
    <button class="btn" @click="stopRecognition">음성 인식 정지</button>
    <div class="text-container">
      <p>인식된 텍스트: {{ transcript }}</p>
    </div>
    <canvas ref="chartCanvas" width="400" height="200"></canvas>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';

export default {
  data() {
    return {
      recognizing: false,
      transcript: '',
      recognitionStartTime: null,
      recognitionDurations: [],
      recognitionChart: null
    };
  },
  created() {
    this.updateChart(); // 페이지가 로드될 때 차트 생성
  },
  methods: {
    startRecognition() {
      if ('SpeechRecognition' in window || 'webkitSpeechRecognition' in window) {
        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        const recognition = new SpeechRecognition();
        recognition.lang = 'ko-KR'; 
        recognition.continuous = true; 
        recognition.interimResults = true; 
        recognition.onstart = () => {
          this.recognizing = true;
          this.recognitionStartTime = Date.now();
          console.log("음성 인식을 시작합니다.");
          // 5초 후에 음성 인식 종료
          setTimeout(() => {
            this.stopRecognition();
          }, 5000);
        };
        recognition.onend = () => {
          this.recognizing = false;
          console.log("음성 인식을 종료합니다.");
          const recognitionDuration = Date.now() - this.recognitionStartTime;
          this.recognitionDurations.push(recognitionDuration);
          this.updateChart(); // 음성 인식이 종료된 후 차트 업데이트
        };
        recognition.onresult = (event) => {
          let interimTranscript = '';
          for (let i = event.resultIndex; i < event.results.length; ++i) {
            if (event.results[i].isFinal) {
              this.transcript = event.results[i][0].transcript;
            } else {
              interimTranscript += event.results[i][0].transcript;
            }
          }
          console.log("중간 결과: " + interimTranscript);
        };
        recognition.start(); // 음성 인식 시작
      } else {
        alert("이 브라우저는 음성 인식을 지원하지 않습니다.");
      }
    },
    stopRecognition() {
      if (this.recognizing) {
        this.recognizing = false;
        console.log("음성 인식을 종료합니다.");
      }
    },
    updateChart() {
      if (!this.recognitionChart) {
        this.recognitionChart = new Chart(this.$refs.chartCanvas, {
          type: 'line',
          data: {
            labels: this.recognitionDurations.map((_, index) => `Attempt ${index + 1}`),
            datasets: [{
              label: '음성 인식 시간(ms)',
              data: this.recognitionDurations,
              backgroundColor: 'rgba(75, 192, 192, 0.2)',
              borderColor: 'rgba(75, 192, 192, 1)',
              borderWidth: 1
            }]
          },
          options: {
            scales: {
              y: {
                beginAtZero: true
              }
            }
          }
        });
      } else {
        this.recognitionChart.data.labels = this.recognitionDurations.map((_, index) => `Attempt ${index + 1}`);
        this.recognitionChart.data.datasets[0].data = this.recognitionDurations;
        this.recognitionChart.update();
      }
    }
  }
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.btn {
  margin: 5px;
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.text-container {
  margin-top: 10px;
  padding: 10px;
  background-color: #f0f0f0;
  border-radius: 5px;
}
</style>
