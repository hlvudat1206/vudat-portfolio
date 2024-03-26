<script>
  let choseLine = 0;
  let imageList = [
    {
      id: 1,
      description:
        "Introducing rendering capability for certain models, which encompasses reading texture files, setting up lighting, and configuring the environment",
      path: "./assets/bio-images/dat1.jpg",
    },
    {
      id: 2,
      description:
        "Introducing a solution that simulates real estate properties, enabling users to easily interact with them. Users can change the color of the house roof, house wall, rotate the house, and take an automated tour to explore the property.",
      path: "./assets/bio-images/dat2.jpg",
    },
    {
      id: 3,
      description:
        "Introducing a solution that simulates real estate properties, enabling users to easily interact with them. Users can change the color of the house roof, house wall, rotate the house, and take an automated tour to explore the property.",
      path: "./assets/bio-images/dat3.jpg",
    },
  ];

  let lineList = [
    {
      id: 1,
      name: "line 1",
      value: 0,
    },
    {
      id: 2,
      name: "line 2",
      value: 1,
    },
    {
      id: 3,
      name: "line 3",
      value: 2,
    },
  ];

  const chooseLine = (line) => {
    console.log("lineeee: ", line);
    choseLine = line.value;
  };

  const autoImages = () => {
    if (choseLine >= 0 && choseLine < 2) {
      choseLine += 1;
    } else {
      choseLine -= 1;
    }
  };

  setInterval(autoImages, 4000);
</script>

<div class="bio-content">
  <div>
    <div class="slider">
      <img src={imageList[choseLine].path} height="75%" width="65%" />
    </div>
  </div>
  <div class="time-line">
    {#each lineList as line}
      <div
        class="line"
        style={line.value == choseLine ? "background-color: #ffffff;" : ""}
        on:click={() => chooseLine(line)}
      ></div>
    {/each}
  </div>
  <div class="bio-text">
    I'm a 3D WebGL Developer with over 2 years of experience in Ho Chi Minh
    City, Vietnam.
  </div>
</div>

<style>
  .line {
    height: 5px;
    width: 50px;
    background-color: #9b9494;
    margin-right: 5px;
  }
  .line:hover {
    background-color: #e3dbdb;
    cursor: pointer;
  }
  .slider {
    position: absolute;
    width: 100%;
    height: 90%;
    overflow: hidden;
    transition: all 3s;
  }
  img {
    border-radius: 50%;
  }

  .time-line {
    display: flex;
    margin-bottom: 2px;
    color: white;
    width: 100%;
    z-index: 9999;
    position: absolute;
    margin-bottom: 20%;
    height: 100%;
    align-items: center;
    justify-content: center;
    margin-top: 25%;
  }

  .bio-text {
    display: flex;
    margin-bottom: 2px;
    color: white;
    width: 100%;
    position: absolute;
    margin-bottom: 20%;
    height: 100%;
    align-items: center;
    justify-content: center;
    margin-top: 35%;
  }
</style>
