<template>
  <div>
    <div class="container">
        <h1>{{  question  }}</h1>
        <label for="answer">Your Answer:</label>
        <input v-model="answerRef" type="text" id="answer" name="answer" placeholder="Type your answer here">
        <button @click="checkSimilarity" :disabled="isFetching" :class="{ 'disabled': isFetching }">{{ isFetching ? 'Loading...' : 'Submit' }}</button>
    </div>

    <div class="result-card">
      <p>Similarity:</p>
      <div class="score">{{ resultScore === null ? '--.-- %' : resultScore.toFixed(2) + ' %' }}</div>
    </div>
  </div>
</template>

<script setup>

const question = 'Mee rebus is better than mee goreng'
const answerRef = ref('')
const isFetching = ref(false)
const resultScore = ref(null);

const checkSimilarity = async () => {
  isFetching.value = true;

  const API_TOKEN = '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';

  const answer = answerRef.value;
  if (!answer) {
    alert('Please enter your answer')
    isFetching.value = false;
    return
  }

  const data = {
    inputs: {
      source_sentence: question,
      sentences: [answer]
    }
  }

  const response = await fetch(
    'https://api-inference.huggingface.co/models/sentence-transformers/all-MiniLM-L6-v2', 
        {
            headers: { 'Authorization': `Bearer ${API_TOKEN}` },
            method: 'POST',
            body: JSON.stringify(data)
        }
  )

  const result = await response.json()
  console.log(`Answer: ${answerRef.value}, Similarity: ${result}`)

  resultScore.value = result;
  if (resultScore.value !== null) {
    resultScore.value = resultScore.value * 100
  }

  isFetching.value = false;
}


</script>

<style scoped>
body {
  background-color: purple;
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  background-color: white;
  padding: 30px; /* Increased padding for more spacing */
  border-radius: 10px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
  text-align: center; /* Center the content horizontally */
  max-width: 600px; /* Limit the width of the container */
  margin: 0 auto; /* Center the container horizontally */
}

h1 {
  color: purple;
  margin-bottom: 20px; /* Increased margin below the heading */
}

label {
  font-weight: bold;
  margin-bottom: 15px; /* Increased margin below the label */
  display: block;
}

input[type="text"] {
  width: 80%;
  padding: 15px; /* Increased padding for the input */
  border: 1px solid #ccc;
  border-radius: 5px;
  outline: none;
  margin-bottom: 20px; /* Increased margin below the input */
}

input[type="text"]:focus {
    border-color: purple;
}

button {
  background-color: purple;
  color: white;
  border: none;
  padding: 15px 30px; /* Increased padding for the button */
  border-radius: 5px;
  cursor: pointer;
  display: block;
  margin: 10px  auto;
}

button:hover {
  background-color: #6a1b9a;
}
.result-card {
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
  text-align: center;
  max-width: 400px;
  margin: 20px auto;
}

.score {
  font-size: 24px;
  font-weight: bold;
  color: purple;
  margin-top: 10px;
}

button.disabled {
  background-color: #ccc; /* Gray out the button */
  cursor: not-allowed; /* Change cursor to not-allowed */
  opacity: 0.6; /* Reduce opacity */
}
</style>