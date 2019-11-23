<template>
  <div style="width: 500px; margin: auto">
    <!-- header -->
    <div style="color: gray">
      <div class="space-between" style="margin:70px 0">
        <div>
          <h1 style="font-size: 2.5em;">2048</h1>
          <p class="game-intro">Join the numbers and get to the <strong>2048 tile!</strong></p>
        </div>
        <div class="flex-center">
          <div class="blue-container" style="padding: 5px 15px; margin-right: 10px;">
            <p>score</p>
            <h2>{{ score }}</h2>
          </div>
          <div class="blue-container" style="padding: 5px 15px;">
            <p>best</p>
            <h2>{{ best }}</h2>
          </div>
        </div>
      </div>
      <p class="button bold" style="margin-bottom: 30px"><i class="icon">add_circle</i> New Game</p>
    </div>

    <div id="game" class="blue-container flex-center" style="width: 500px; height: 500px; flex-wrap: wrap;">
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span> 
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
      <span>0</span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      score: 0,
      best: 0
    };
  },
  methods: {
    init() {
      console.log("eeeeeeeeee");
    }
  },
  mounted() {
    this.init();
  }
};
</script>
