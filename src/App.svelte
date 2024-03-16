<script>
  import * as THREE from "@build/three.module";
  import { onMount, tick } from "svelte";

  import { Viewer } from "./lib/scene-config/viewer";
  import queryString from "@js/query-string-main/index.js";
  import WebGL from "@js/WebGL.js";
  import Slider from "./content/slider.svelte";
  import { TransformControls } from "@js/TransformControls.js";

  let scene;
  let selectedModel = "./assets/models/cyberCity/scene.gltf";

  let viewer;
  let canvas;
  let isGoMallMode = false;
  let textButtonView = "Scroll Mode";

  let options;
  $: console.log("isGoMallMode: ", isGoMallMode);
  window.VIEWER = {};

  if (!(window.File && window.FileReader && window.FileList && window.Blob)) {
    console.error("The File APIs are not fully supported in this browser.");
  } else if (!WebGL.isWebGLAvailable()) {
    console.error("WebGL is not supported in this browser.");
  }
  const init = () => {
    const hash = location.hash ? queryString.parse(location.hash) : {};
    console.log("hasssh: ", hash, location);
    options = {
      kiosk: Boolean(hash.kiosk),
      model: hash.model || "",
      preset: hash.preset || "",
      cameraPosition: hash.cameraPosition
        ? hash.cameraPosition.split(",").map(Number)
        : null,
    };
    // canvas.width = window.innerWidth; // Set width to full window width
    // canvas.height = window.innerHeight; // Set height to full window height
    let container = document.getElementById("container");

    scene = new THREE.Scene();
    viewer = new Viewer(container, options);
    loadModel(selectedModel);

    // // //create planeMesh
    // const visiablePlane = new THREE.Mesh(
    //   new THREE.PlaneGeometry(400, 200),
    //   new THREE.MeshBasicMaterial({
    //     color: 0x6f7a73,
    //     side: THREE.DoubleSide,
    //     visible: true,
    //   })
    // );

    // visiablePlane.rotateX(Math.PI / 2);
    // // visiablePlane.scale.set(1000, 1000, 1000);
    // visiablePlane.name = "ground";
    // visiablePlane.position.set(0, -3.5, 0);

    // viewer.createObject(visiablePlane);
    /////
    const mainLayer = document.getElementById("main");
    // Create a new child element
    const canvasThree = viewer.rendererDom();
    const projectArea = document.getElementById("built-projects");
    const bioArea = document.getElementById("bio-area");
    const skillArea = document.getElementById("skill-area");
    const middleArea = document.getElementById("middle-area");
    // Get the first child of the parent
    const canvas = mainLayer.firstChild;
    // Insert the new child before the first child
    mainLayer.insertBefore(canvasThree, canvas);
    mainLayer.insertBefore(middleArea, canvasThree);

    mainLayer.insertBefore(projectArea, canvasThree);
    mainLayer.insertBefore(bioArea, projectArea);
    mainLayer.insertBefore(skillArea, projectArea);
    mainLayer.insertBefore(middleArea, canvasThree);

    const axesLayer = viewer.axesDom();
    mainLayer.insertBefore(axesLayer, canvas);
    const guiLayer = viewer.guiDom();
    mainLayer.insertBefore(guiLayer, canvas);

    const scrollDemo = document.querySelector("#main");
    const output = document.querySelector(".output");
  };

  function loadModel(path) {
    console.log("vao load 1");
    view(path);
    // sendData()
  }

  /**
   * @param  {Error} error
   */
  function onError(error) {
    let message = (error || {}).message || error.toString();
    if (message.match(/ProgressEvent/)) {
      message =
        "Unable to retrieve this file. Check JS console and browser network tab.";
    } else if (message.match(/Unexpected token/)) {
      message = `Unable to parse file content. Verify that this file is valid. Error: "${message}"`;
    } else if (error && error.target && error.target instanceof Image) {
      message = "Missing texture: " + error.target.src.split("/").pop();
    }
    window.alert(message);
    console.error(error);
  }

  function view(path) {
    let fileURL = path;
    viewer
      .load(fileURL)
      .catch((e) => {
        console.log("err: ", e);
        return onError(e);
      })
      .then((gltf) => {
        if (!options.kiosk) {
          // this.validationCtrl.validate(fileURL, rootPath, fileMap, gltf);
        }
        console.log("gltff: ", gltf);
        // cleanup();
      });
    // console.log("in rootPath: ", rootPath);
    // console.log("in fileMap: ", fileMap);
  }

  const onOverViewButton = () => {
    isGoMallMode = !isGoMallMode;
    viewer.updateStatusScroll(isGoMallMode);
  };
  const interactObject = () => {
    console.log("ckick click");
    console.log("viewer.getCoordinate(): ", viewer.getCoordinate());
  };



  onMount(() => {
    init();
  });
</script>

<main
  id="main"
  style="display: flex;
    flex-direction: column-reverse;"
  on:click={interactObject}
>
  <canvas class="full-screen" id="container" bind:this={canvas}> </canvas>
  <div id="built-projects">
    <div
      style="font-weight: 600;
    font-size: 20px;
    text-align: left;
    left: 10px;
    color: #009999;
    background-color:white
    "
    >
      3D Projects
    </div>
    <div style="background-color:gray">
      <Slider></Slider>
    </div>
  </div>
  <div class="middle-main" id="middle-area">
    <div class="view-button">
      <button
        style="--focus-color: {isGoMallMode
          ? '#1d6291'
          : '#9c460e'}; --focus-border: {isGoMallMode
          ? '2px solid blue'
          : 'none'}"
        on:click={onOverViewButton}>{textButtonView}</button
      >
    </div>
  </div>
  <div class="broken-border" id="bio-area">
    <div class="title-image">Introduction</div>
    <div class="image-text">
      I'm Vu Dat, a graduate with a degree in engineering physics and
      mathematics from Ho Chi Minh City University of Technology. With over 2
      years of experience in both frontend and backend web development, I've
      contributed to various 2D and 3D web projects, including traditional
      management software, 3D real estate, and shopping malls. My focus lies in
      enhancing user experience by researching and optimizing 3D model
      materials, lighting, backgrounds, and more for web displays
    </div>
  </div>

  <div class="broken-border-v2" id="skill-area">
    <div class="title-image">My Skill and Experience</div>
    <div class="image-text">
      <div>
        Design and Develop Web App From 2D Web Elements to 3D Web Integrations
      </div>
      <br />
      <div>
        <div>2D: Frontend: Svelte, Reactjs, Vanilla Javascript</div>
        <div>Backend: Golang, Nodejs, Git, Gitlab</div>
      </div>
      <br />
      <br />
      <div>3D: Three js Tool: Blender</div>
      <div
        class="icon-box"
        style="position: absolute;
    width: 70%;
    display: flex;
    justify-content: space-between;
    align-items: center;"
      >
        <div class="js-icon"></div>
        <div class="svelte-icon"></div>
        <div class="threejs-icon"></div>
        <div class="git-icon"></div>
      </div>
    </div>
  </div>
</main>

<!-- <SimpleModal
  bind:this={simpleModalRef}
  heightSize={"250px"}
  on:clickButton={showInfoModel}
  on:closeButton={closeInfomodel}
  saveButtonName={"Save AA"}
  bind:showModal
>
  <div slot="content">
    <div>
      Áo Thun Trơn Áo Phông Trắng Đen Xám Nam Nữ Form Xuông Vải Dày Mịn Không Xù
      Lông
    </div>
    <div class="detail-product">
      <Split initialPrimarySize="70%">
        <div slot="primary" class="left-content">
          <div>Price</div>
          <div>About Product</div>
          <div>Orgin</div>
          <div>Color</div>
          <div>Size</div>
        </div>
        <div slot="secondary" class="right-content">haha</div>
      </Split>
    </div>
  </div>
</SimpleModal> -->

<style>
  #container {
  }
  #main {
    width: 100%;
    height: 100%;
    flex-grow: 1;
    flex-shrink: 1;
    position: absolute;
    top: 0;
    z-index: 0;
  }

  .view-button {
    left: 50%;
    position: absolute;

    transform: translate(-50%, -50%);
    z-index: 1000;
    padding-bottom: 50px;
  }

  .broken-border {
    width: 500px; /* Adjust as needed */
    height: 200px; /* Adjust as needed */
    background-image: url("./public/assets/frame/frame.png");
    background-size: cover;
    background-position: center;
    position: absolute;
    left: 10%;
    top: 20%;
    min-width: 400px;
    min-height: 400px;
  }
  .middle-main {
  }

  button {
    padding: 10px;
    background-color: var(--focus-color);
    color: white;
    cursor: pointer;
    border: var(--focus-border);

    outline: none;
    overflow: hidden;
    transition: background-color 0.3s; /* Added transition for smooth color change */
  }

  button:hover {
    background-color: rgb(231, 170, 116);
  }
  .image-text {
    font-weight: 600;
    color: #1b4242;
    text-align: left;
    margin: 14%;
    font-size: 14px;
    margin-top: 4%;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
    padding: 10px;
    border-radius: 10px;
    background-color: #e7e792;
  }

  .broken-border-v2 {
    width: 300px; /* Adjust as needed */
    height: 200px; /* Adjust as needed */
    background-image: url("./public/assets/frame/square-frame.png");
    background-size: cover;
    background-position: center;
    position: absolute;
    right: 10%;
    top: 20%;
    min-width: 400px;
    min-height: 400px;
  }
  .js-icon {
    background-image: url("./public/assets/icon/js.svg");
    width: 30px;
    height: 30px;
  }
  .js-icon:hover {
    background-color: aliceblue;
    opacity: 50%;
    border-radius: 6px;
  }
  .svelte-icon {
    background-image: url("./public/assets/icon/svelte.svg");
    width: 80px;
    height: 30px;
  }
  .svelte-icon:hover {
    background-color: aliceblue;
    opacity: 50%;
    border-radius: 6px;
  }

  .threejs-icon {
    background-image: url("./public/assets/icon/threejs.svg");
    width: 30px;
    height: 30px;
  }
  .threejs-icon:hover {
    background-color: aliceblue;
    opacity: 50%;
    border-radius: 6px;
  }
  .git-icon {
    background-image: url("./public/assets/icon/git.svg");
    width: 80px;
    height: 30px;
  }
  .git-icon:hover {
    background-color: rgb(255, 255, 255);
    opacity: 50%;
    border-radius: 6px;
  }
  .icon-box {
    border-radius: 4px;
    border: 0.25px solid #e9d4d4;
  }

  .title-image {
    margin: 0 5% 0 5%;
    font-size: 18px;
    color: #f4a536;
    background-color: #277a7a;
    border-radius: 10px;
    border: 2px solid #065ec0;
  }
</style>
