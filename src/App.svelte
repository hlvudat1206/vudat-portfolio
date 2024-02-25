<script>
  import * as THREE from "@build/three.module";
  import { onMount, tick } from "svelte";

  import { Viewer } from "./lib/scene-config/viewer";
  import queryString from "@js/query-string-main/index.js";
  import WebGL from "@js/WebGL.js";

  let scene;
  // let selectedModel = "src/models/thoitrang_nam_1fittingroom.gltf";
  let selectedModel = "./assets/models/cyberCity/scene.gltf";

  let viewer;
  let canvas;

  let options;
  let projectList = [
    {
      id: 1,
      name: "Rendering Tool",
      description: "",
    },
    {
      id: 2,
      name: "Real Estate Simulation",
      description: "",
    },
    {
      id: 3,
      name: "Doll House",
      description: "",
    },
  ];
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

    // //create planeMesh
    // const visiablePlane = new THREE.Mesh(
    //   new THREE.PlaneGeometry(400, 200),
    //   new THREE.MeshBasicMaterial({
    //     color: 0x6f7a73,
    //     side: THREE.DoubleSide,
    //     visible: true,
    //   })
    // );

    // visiablePlane.rotateX(Math.PI / 2);
    // visiablePlane.name = "ground";
    // visiablePlane.position.set(0, -3.5, 0);

    // viewer.createObject(visiablePlane);

    const mainLayer = document.getElementById("main");
    // Create a new child element
    const canvasThree = viewer.rendererDom();
    const projectArea = document.getElementById("built-projects");
    const bioArea = document.getElementById("bio-area");
    const skillArea = document.getElementById("skill-area");
    // Get the first child of the parent
    const canvas = mainLayer.firstChild;
    // Insert the new child before the first child
    mainLayer.insertBefore(canvasThree, canvas);
    mainLayer.insertBefore(projectArea, canvasThree);
    mainLayer.insertBefore(bioArea, projectArea);
    mainLayer.insertBefore(skillArea, projectArea);

    const axesLayer = viewer.axesDom();
    mainLayer.insertBefore(axesLayer, canvas);
    const guiLayer = viewer.guiDom();
    mainLayer.insertBefore(guiLayer, canvas);

    //set style
    canvasThree.style = "border-radius:25px";
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
    // console.log('in rootPath: ',rootPath)
    // console.log('in fileMap: ',fileMap)
  }

  onMount(() => {
    init();
  });
</script>

<div class="view-button">
  <!-- <button
    style="--focus-color: {isGoMallMode
      ? '#1d6291'
      : '#1f9bed'}; --focus-border: {isGoMallMode ? '2px solid blue' : 'none'}"
    on:click={onOverViewButton}>{textButtonView}</button
  > -->
</div>
<main
  id="main"
  style="display: flex;
    flex-direction: column-reverse;"
>
  <canvas class="full-screen" id="container" bind:this={canvas}> </canvas>
  <div id="built-projects">
    <div
      style="font-weight: 600;
    font-size: 20px;
    margin-left: 20px;
    text-align: left;
    left: 10px;
    color: #009999;"
    >
      3D Projects
    </div>
    <div class="project-card">
      {#each projectList as item, key}
        <div>
          <div class="header-card">
            {item.name}
          </div>
          <div>
            Lorem ipsum dolor sit amet consectetur adipisicing elit. Nam enim
            perferendis temporibus, recusandae voluptates placeat quae? Iusto
            error sint mollitia soluta quibusdam, laborum neque distinctio.
            Culpa exercitationem aliquam nihil voluptatem.
          </div>
        </div>
      {/each}
    </div>
  </div>
  <div class="bio-area" id="bio-area">My Bio</div>

  <div class="skill-area" id="skill-area">My Skill and Experience</div>
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
    position: fixed;
    bottom: 20px;
    transform: translate(-50%, -50%);
    z-index: 1000; /* Ensure the button appears on top of the canvas */
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
    background-color: #74b9e7;
  }

  .left-content {
    /* height: 100%; */
  }

  .right-content {
    /* height: 100%; */
  }

  .detail-product {
    /* width: 100%;
    position: absolute;
    height: 100%; */
  }

  .project-card {
    display: flex;
  }
</style>
