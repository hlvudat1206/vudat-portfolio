<script>
  import * as THREE from "@build/three.module";
  import { onMount, tick } from "svelte";

  import { Viewer } from "./lib/scene-config/viewer";
  import queryString from "@js/query-string-main/index.js";
  import WebGL from "@js/WebGL.js";
  import Slider from "./content/slider.svelte";
  import { TransformControls } from "@js/TransformControls.js";
  import { percentLoading } from "./lib/scene-config/store.js";
  import Tabs from "./components/tab/index.svelte";
  import Bio from "./content/bio.svelte";
  import Skills from "./content/skills.svelte";
  let scene;
  let selectedModel = "./assets/models/cyberCity/scene.gltf";

  let viewer;
  let canvas;
  let isGoMallMode = false;
  let textButtonView = "Scroll Mode";
  let onMenu = true;
  let items = [
    { label: "Introduce", value: 1, component: Bio },
    { label: "Skills", value: 2, component: Skills },
  ];
  let options;
  $: console.log("isGoMallMode: ", isGoMallMode);
  $: console.log("$percentLoading: ", $percentLoading);
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
    // const guiLayer = viewer.guiDom();
    // mainLayer.insertBefore(guiLayer, canvas);

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
    onMenu = !onMenu;
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
    <div class="progress-bar" style="width: {$percentLoading * 100}%;"></div>
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
    <aside class:onMenu>
      <Tabs {items} />
    </aside>
  </div>

  <div class="broken-border-v2" id="skill-area"></div>
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
    /* width: 500px; 
    height: 200px; 
    background-size: cover;
    background-position: center;
    position: absolute;
    left: 10%;
    top: 20%;
    min-width: 400px;
    min-height: 400px; */
    /* background-image: url("./public/assets/frame/frame.png"); */
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

  .broken-border-v2 {
    width: 300px; /* Adjust as needed */
    height: 200px; /* Adjust as needed */
    /* background-image: url("./public/assets/frame/square-frame.png"); */
    background-size: cover;
    background-position: center;
    position: absolute;
    right: 10%;
    top: 20%;
    min-width: 400px;
    min-height: 400px;
  }

  .progress-bar {
    height: 8px;
    background-color: rgba(12, 144, 36, 0.76);
    transition: width 0.3s ease-in-out;
    border-radius: 5pxrgba (32, 184, 60, 0.76);
  }

  aside {
    position: absolute;
    left: -500px;
    transition: all 0.5s;

    height: 50%;
    width: 20%;
    top: 10%;
    bottom: 20%;
    border: 1px solid #ddd;
    background-color: #ffe7e7;
    border-radius: 12px;
    margin-top: 20px;
    cursor: pointer;
  }

  .onMenu {
    left: 0px;
    cursor: pointer;
  }
</style>
