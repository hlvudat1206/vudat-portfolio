<script>
  let currentSlide;
  $: console.log("currentSlide: ", currentSlide);
  let projectList = [
    {
      id: 1,
      name: "Rendering Tool",
      description: "",
      path: "./public/assets/project-thumbnail/render.png",
    },
    {
      id: 2,
      name: "Real Estate Simulation",
      description: "",
      path: "./public/assets/project-thumbnail/smart-home.png",
    },
    {
      id: 3,
      name: "Doll House",
      description: "",
      path: "./public/assets/project-thumbnail/doll-house.png",
    },
    {
      id: 2,
      name: "Real Estate 2",
      description: "",
      path: "",
    },
    {
      id: 3,
      name: "Doll 2",
      description: "",
      path: "",
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
      translateSlider(slides[currentSlide].offsetWidth);
    }

    function handlePrevClick() {
      currentSlide -= 1;
      if (currentSlide < 0) {
        currentSlide = slides.length - 1 - lastIndexCard; // Set to last slide for infinite scrolling
      }

      translateSlider(slides[currentSlide].offsetWidth);
    }

    function handleNextClick() {
      currentSlide += 1;
      if (currentSlide + lastIndexCard >= slides.length) {
        currentSlide = 0; // Set to first slide for infinite scrolling
        // projectList = projectList.reverse();
      }
      translateSlider(slides[currentSlide].offsetWidth);
    }

    slider.addEventListener("click", handleSlideClick);
    prevButton.addEventListener("click", handlePrevClick);
    nextButton.addEventListener("click", handleNextClick);

    // Initial setup
    translateSlider(slides[currentSlide].offsetWidth);
  });
</script>

<div class="slider-container">
  <div class="slider">
    {#each projectList as item, key}
      <div class="slide">
        <div style="display:flex">
          <div class="image-custom">
            <img src={item.path} height="80%" width="100%" />
            <div class="image-caption">
              {item.name}
            </div>
          </div>
          <div class="content">
            <div>
              Lorem ipsum dolor sit amet consectetur adipisicing elit.
              Voluptatem iure explicabo repellendus, cum totam consequuntur
              laboriosam quae tempora labore, doloremque magni. Magni porro at,
              animi perspiciatis sint quaerat nobis doloremque?
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
  }

  .slider {
    display: flex;
    transition: transform 0.5s ease-in-out;
  }

  .slide {
    flex: 0 0 33.33%; /* Set width to 1/3rd of parent element */
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
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); /* Box shadow */
    display: flex;
    flex-direction: column;
  }

  /* Image styling */
  .image-custom img {
    transition: transform 0.3s ease; /* Smooth transition */
  }

  /* Image hover effect */
  .image-custom:hover img {
    transform: scale(1.05);
  }

  /* Image caption */
  .image-caption {
    /* position: absolute; */
    bottom: 0;
    left: 0;
    /* width: 100%; */
    padding: 10px;
    background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent background */
    color: #fff; /* Text color */
    font-size: 16px;
  }
</style>
