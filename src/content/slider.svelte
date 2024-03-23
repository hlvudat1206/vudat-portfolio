<script>
  let currentSlide;
  $: console.log("currentSlide: ", currentSlide);
  let projectList = [
    {
      id: 1,
      name: "Rendering Tool",
      description:
        "Introducing rendering capability for certain models, which encompasses reading texture files, setting up lighting, and configuring the environment",
      path: "./assets/project-thumbnail/render.png",
      link: "https://hlvudat1206.github.io/render-model-dp",
    },
    {
      id: 2,
      name: "Real Estate Simulation",
      description:
        "Introducing a solution that simulates real estate properties, enabling users to easily interact with them. Users can change the color of the house roof, house wall, rotate the house, and take an automated tour to explore the property.",
      path: "./assets/project-thumbnail/smart-home.png",
      link: "https://hlvudat1206.github.io/3dsmarthome",
    },
    {
      id: 3,
      name: "Doll House",
      description:
        "Introducing a simulation solution for houses, which includes the ability to move within the house and measure the size of certain furniture items.",
      path: "./assets/project-thumbnail/doll-house.png",
      link: "https://sanpham.starglobal3d.com/demo/house3d/",
    },
    {
      id: 4,
      name: "Dat's Portfolio",
      description: "Introduce some projects experienced by Dat.",
      path: "./assets/project-thumbnail/portfolio.png",
      link: "https://hlvudat1206.github.io/vudat-portfolio/",
    },
  ];

  document.addEventListener("DOMContentLoaded", function () {
    const slider = document.querySelector(".slider");
    const slides = document.querySelectorAll(".slide");
    const prevButton = document.querySelector(".prev-slide");
    const nextButton = document.querySelector(".next-slide");

    currentSlide = 0;
    let lastIndexCard = 2; //having 3 element in card

    function translateSlider(amount) {
      slider.style.transform = `translateX(-${currentSlide * amount}px)`;
    }

    function handleSlideClick(event) {
      const clickedSlide = event.target.closest(".slide");
      if (!clickedSlide) return; // Ignore clicks outside of slides
      currentSlide = parseInt(clickedSlide.dataset.index, 10);
      translateSlider(slides[currentSlide] && slides[currentSlide].offsetWidth);
    }

    function handlePrevClick() {
      currentSlide -= 1;
      if (currentSlide < 0) {
        currentSlide = slides.length - 1 - lastIndexCard; // Set to last slide for infinite scrolling
      }

      translateSlider(slides[currentSlide] && slides[currentSlide].offsetWidth);
    }

    function handleNextClick() {
      console.log("click right");
      currentSlide += 1;
      if (currentSlide + lastIndexCard >= slides.length) {
        currentSlide = 0; // Set to first slide for infinite scrolling
        // projectList = projectList.reverse();
      }
      translateSlider(slides[currentSlide] && slides[currentSlide].offsetWidth);
    }

    slider.addEventListener("click", handleSlideClick);
    prevButton.addEventListener("click", handlePrevClick);
    nextButton.addEventListener("click", handleNextClick);

    // Initial setup
    translateSlider(slides[currentSlide].offsetWidth);
  });

  const openWindow = (item) => {
    // e.preventDefault();

    var url = item.link;

    //open mini window
    // var features = "width=600,height=400,resizable=yes";
    // window.open(url, "_blank", features);

    //open direct
    // window.location.href = url;

    window.open(url);
  };
</script>

<div class="slider-container">
  <div class="slider">
    {#each projectList as item, key}
      <div class="slide">
        <div style="display:flex; height:100%">
          <div class="image-custom">
            <div>
              <img
                src={item.path}
                height="100%"
                width="100%"
                on:click={() => openWindow(item)}
              />
            </div>
            <div class="image-caption">
              {item.name}
            </div>
          </div>
          <div class="content">
            <div>
              {item.description}
            </div>
          </div>
        </div>
      </div>
    {/each}
  </div>
  <div class="slider-controls">
    <button class="prev-slide">&lt;</button>

    <button class="next-slide">&gt;</button>
  </div>
</div>

<style>
  .slider-controls {
    display: flex;
    align-items: center;
  }
  .slider-container {
    display: flex;
    overflow: hidden;
    /* Optional, set a maximum width if needed */
    min-height: 200px;
    max-height: 250px;
  }

  .slider {
    display: flex;
    transition: transform 0.5s ease-in-out;
  }

  .slide {
    border: 2px solid;
    flex: 0 0 calc(33.33% - 8px);
    margin: 5px 2px 5px 2px; /* Optional spacing between cards */
    border-radius: 5px; /* Add rounded corners */
    background-color: #fff; /* Set background color */
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1); /* Add subtle shadow */
    text-align: center;
  }

  .prev-slide,
  .next-slide {
    position: absolute;

    /* transform: translateY(-50%); */
    background: rgba(0, 0, 0, 0.5);
    color: white;
    border: none;
    outline: none;
    cursor: pointer;
    font-size: 24px;
    padding: 10px;
    z-index: 2;
  }

  .prev-slide {
    left: 0;
  }

  .next-slide {
    right: 0;
  }

  .prev-slide:hover,
  .next-slide:hover {
    background-color: rgba(255, 255, 255, 0.5);
    color: black;
  }

  .image-custom {
    position: relative;
    overflow: hidden;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    display: flex;
    flex-wrap: wrap;
    height: 100%;
    flex-direction: row-reverse;
    flex: 60%;
  }

  /* Image styling */
  .image-custom img {
    transition: transform 0.3s ease; /* Smooth transition */
  }

  /* Image hover effect */
  .image-custom:hover img {
    transform: scale(1.05);
  }
  .image-custom:hover {
    background-color: #efd699;
    cursor: pointer;
  }

  /* Image caption */
  .image-caption {
    /* position: absolute; */
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent background */
    color: #fff; /* Text color */
    font-size: 16px;
    width: 100%;
  }

  .content {
    flex: 40%;
    font-weight: 500;
    background-color: wheat;
  }
</style>
