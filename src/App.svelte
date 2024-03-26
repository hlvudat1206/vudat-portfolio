<script>
  import * as THREE from "@build/three.module";
  import { onMount, tick } from "svelte";

  import { Viewer } from "./lib/scene-config/viewer";
  import queryString from "@js/query-string-main/index.js";
  import WebGL from "@js/WebGL.js";
  import Slider from "./content/slider.svelte";
  import { TransformControls } from "@js/TransformControls.js";
  import {
    percentLoading,
    menuStatus,
    contactStatus,
    rightMenuStatus,
  } from "./lib/scene-config/store.js";
  import Tabs from "./components/tab/index.svelte";
  import Bio from "./content/bio.svelte";
  import Skills from "./content/skills.svelte";
  import { tooltip } from "./components/tooltip/tooltip";
  let scene;
  let selectedModel = "./assets/models/cyberCity/scene.gltf";
  let infoUrlPng = "./assets/icon/info-icon.png";
  import BioImage from "./content/bio-image.svelte";

  let viewer;
  let canvas;
  let isGoMallMode = false;
  let textButtonView = "Scroll Mode";
  let onMenu = true;
  let onContactBar = true;
  let onRightMenu = true;
  let items = [
    { label: "Introduce", value: 1, component: Bio },
    { label: "Skills", value: 2, component: Skills },
  ];
  let options;
  let iconField;

  $: console.log("isGoMallMode: ", isGoMallMode);
  $: console.log("$percentLoading: ", $percentLoading);
  window.VIEWER = {};

  $: onMenu = $menuStatus;
  $: onRightMenu = $rightMenuStatus;
  $: console.log("contactStatus: ", $contactStatus),
    (onContactBar = $contactStatus);

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

    const middleArea = document.getElementById("middle-area");
    const contactBar = document.getElementById("contact-bar");
    // Get the first child of the parent
    const canvas = mainLayer.firstChild;
    // Insert the new child before the first child
    mainLayer.insertBefore(canvasThree, canvas);
    mainLayer.insertBefore(contactBar, canvasThree);

    mainLayer.insertBefore(middleArea, canvasThree);

    mainLayer.insertBefore(projectArea, canvasThree);

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

  function loadIcontoObject(icon, pos) {
    return viewer.icon(icon, pos);
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
    onContactBar = !onContactBar;
    onRightMenu = !onRightMenu;
    viewer.updateStatusScroll(isGoMallMode);
  };
  const interactObject = () => {
    console.log("ckick click");
    console.log("viewer.getCoordinate(): ", viewer.getCoordinate());
  };

  const onMenuBar = () => {
    onMenu = !onMenu;
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
  <div
    class="contact-bar {onContactBar ? '' : 'contact-style'}"
    id="contact-bar"
  >
    <div class="mail-icon contact-bar__hover">
      <span title="dathuynh001@gmail.com" use:tooltip
        ><a href="/" target="_blank"></a>
      </span>
    </div>
    <div class="phone-icon contact-bar__hover">
      <span title="0908936451" use:tooltip
        ><a href="/" target="_blank"></a></span
      >
    </div>
    <div class="instagram-icon contact-bar__hover">
      <span title="@vudat.huynh1206/" use:tooltip
        ><a href="https://www.instagram.com/vudat.huynh1206/" target="_blank"
        ></a></span
      >
    </div>

    <div class="discord-icon contact-bar__hover">
      <span title="@740394106978697337" use:tooltip
        ><a
          href="https://discordapp.com/users/740394106978697337"
          target="_blank"
        ></a></span
      >
    </div>
  </div>

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
          : '#b3b10f'}; --focus-border: {isGoMallMode
          ? '2px solid blue'
          : 'none'}"
        on:click={onOverViewButton}>{textButtonView}</button
      >
    </div>
  </div>
  <aside class:onMenu>
    <div class="back-icon" on:click={onMenuBar}></div>

    <Tabs {items} />
  </aside>
  <div class="left-bar {onMenu ? '' : 'onMenu'}">
    <div class="next-icon" on:click={onMenuBar}></div>
  </div>

  <rightside class:onRightMenu> <BioImage></BioImage> </rightside>
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
    background-color: #ddda5a;
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
    left: -1000px;
    transition: all 1s;
    margin-left: 15px;
    height: 45%;
    width: 35%;
    top: 10%;
    bottom: 20%;
    border: 2px solid #ddd;
    background-color: #56514f;
    color: #ddd;
    border-radius: 12px;
    margin-top: 20px;
  }

  rightside {
    position: absolute;
    right: -1000px;
    transition: all 1s;
    margin-left: 15px;
    height: 45%;
    width: 25%;
    top: 10%;
    bottom: 20%;
    border: 2px solid #ddd;
    background-color: #56514f;
    color: #ddd;
    border-radius: 12px;
    margin-top: 20px;
    margin-right: 10px;
  }

  .onRightMenu {
    right: 0px;
  }
  .onMenu {
    left: 0px !important;
  }

  /* .contact-bar {
    background-color: aqua;
    height: 100px;
    width: 300px;
    right: 20px;
    top: 0px;
    position: absolute;
  } */

  .contact-bar {
    height: 72px;
    position: relative;
    transition: all 2s;
    background-size: 100%;
    background-attachment: fixed;
    border: 0px solid rgba(255, 255, 255, 0.1);
    top: 10px;
    right: 10px;
    color: #363636;
    text-align: center;
    font: sans-serif;
    font-weight: 600;
    padding: 4px;
    display: flex;
    align-items: center;
    position: absolute;
    justify-content: center;
    height: 5%;
    right: 15%;
    border-radius: 10px;
    background-color: #e7e1d0;
    box-shadow: 0 0 4px black;
    -webkit-transition: all 0.25s cubic-bezier(0.52, 0.76, 0.52, 0.76);
    -moz-transition: all 0.25s cubic-bezier(0.52, 0.76, 0.52, 0.76);
    -o-transition: all 0.25s cubic-bezier(0.52, 0.76, 0.52, 0.76);
    -ms-transition: all 0.25s cubic-bezier(0.52, 0.76, 0.52, 0.76);
    transition: all 0.25s cubic-bezier(0.52, 0.76, 0.52, 0.76);
  }
  .contact-bar:before {
    display: block;
    position: absolute;

    -webkit-filter: blur(20px);
    -moz-filter: blur(15px);
    -o-filter: blur(15px);
    -ms-filter: blur(15px);
    filter: url(#blurLayer);
    filter: blur(15px);
    opacity: 0.9;
    content: " ";
    background: lightblue url("@public/assets/bluesky.jpg") no-repeat fixed
      center;
    background-size: cover;
  }

  .contact-style {
    top: -100px;
  }

  .mail-icon {
    background-image: url("@public/assets/icon/email.svg");
    width: 80px;
    height: 30px;
  }
  .phone-icon {
    background-image: url("@public/assets/icon/phone.svg");
    width: 80px;
    height: 30px;
  }

  .instagram-icon {
    background-image: url("@public/assets/icon/instagram.svg");
    width: 80px;
    height: 30px;
  }

  .discord-icon {
    background-image: url("@public/assets/icon/discord.svg");
    width: 80px;
    height: 30px;
  }

  .back-icon {
    background-image: url("@public/assets/icon/back.svg");
    width: 80px;
    height: 30px;
    margin-top: 5px;
    border-radius: 5px;
    background-color: white;
    right: 5px;
    position: absolute;
    text-align: center;
  }
  .back-icon:hover {
    background-color: wheat;
    cursor: pointer;
  }

  .contact-bar__hover:hover {
    cursor: pointer;
    background-color: rgb(194, 200, 141);
    border-radius: 5px;
    height: 100%;
  }

  .left-bar {
    position: absolute;
    left: -300px;
    transition: all 1s;
    height: 45%;
    width: 5%;
    top: 10%;
    bottom: 20%;
    border: 2px solid #ddd;
    background-color: #56514f;
    color: #ddd;
    border-radius: 12px;
    margin-top: 20px;
  }

  .next-icon {
    background-image: url("@public/assets/icon/next.svg");
    /* height: 30px;
    margin-top: 5px;
    border-radius: 5px;
    background-color: white;
    right: 5px;
    left: 5px;
    position: absolute;
    text-align: center; */
    height: 30px;
    margin-top: 5px;
    border-radius: 5px;
    background-color: white;
    right: 5px;
    left: 5px;
    position: absolute;
  }

  .next-icon:hover {
    background-color: wheat;
    cursor: pointer;
  }
</style>
