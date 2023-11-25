<template>
  <div>
    <h1>Air Quality Information</h1>
    <table class="table">
      <thead>
        <tr>
          <th>Time</th>
          <th v-for="(value, key) in airQualityData[0]" :key="key">{{ value }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, time) in airQualityData" :key="time">
          <td>{{ time }}</td>
          <td v-for="(value, key) in data" :key="key">{{ value }}</td>
        </tr>
      </tbody>
     
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      airQualityData: {}
    };
  },
  mounted() {
    //const apiKey = '여기에_인증키_붙여넣기';
    const apiUrl = `https://apis.data.go.kr/B552584/ArpltnInforInqireSvc/getMsrstnAcctoRltmMesureDnsty?serviceKey=UG%2FoFdnta3Lg9FqBzA2vT8JPVkuPIX99gvnSZvXQ8aJnMRwzbLT6or1kf5l6bOSOSX2CgVF6MrBQqX7cgpybDA%3D%3D&returnType=xml&numOfRows=100&pageNo=1&stationName=%EC%A2%85%EB%A1%9C%EA%B5%AC&dataTerm=DAILY&ver=1.0`;

    axios.get(apiUrl)
  .then(response => {
    const parser = new DOMParser();
    const xmlDoc = parser.parseFromString(response.data, 'text/xml');
    const items = xmlDoc.getElementsByTagName('item');

    const data = {};

    for (let i = 0; i < items.length; i++) {
      const item = items[i];
      const dataTime = item.getElementsByTagName('dataTime')[0].textContent;
      const measurements = {};

      // 측정 항목과 측정값을 객체에 저장
      const itemChildNodes = item.childNodes;
      for (let j = 0; j < itemChildNodes.length; j++) {
        const childNode = itemChildNodes[j];
        if (childNode.nodeType === 1 && childNode.nodeName !== 'dataTime') {
          measurements[childNode.nodeName] = childNode.textContent;
        }
      }

      data[dataTime] = measurements;
    }

    this.airQualityData = data;
  })
  .catch(error => {
    console.error(error);
  });

  }
};
</script>
<style>
/* Bootstrap 스타일링 예시 - 사용하시는 CSS 프레임워크 또는 자체 스타일에 맞게 수정하세요 */
.table {
  width: 100%;
  margin-bottom: 1rem;
  color: #212529;
  border-collapse: collapse;
}

.table th,
.table td {
  padding: 0.75rem;
  vertical-align: top;
  border-top: 1px solid #dee2e6;
}

.table thead th {
  vertical-align: bottom;
  border-bottom: 2px solid #dee2e6;
}

.table tbody + tbody {
  border-top: 2px solid #dee2e6;
}
</style>