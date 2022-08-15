<template>
  <div id="app">
    <div class="left">
      <ScaffoldVuer
        ref="scaffold1"
        :displayMarkers="displayMarkers"
        :display-u-i="displayUI"
        :url="viewer_1.url"
        :format="viewer_1.format"
      />
    </div>
    <div class="right">
      <ScaffoldVuer
        ref="scaffold2"
        :displayMarkers="displayMarkers"
        :display-u-i="displayUI"
        :url="viewer_2.url"
        :format="viewer_2.format"
        @on-ready="onReady"
      />
    </div>
  </div>
</template>

<script>
/* eslint-disable no-alert, no-console */
import { ScaffoldVuer } from '@abi-software/scaffoldvuer';
import '@abi-software/scaffoldvuer/dist/scaffoldvuer.css';

export default {
  name: "App",
  components: {
    ScaffoldVuer
  },
  data: function() {
    return {
      displayUI: true,
      displayMarkers: false,
      viewer_1 : {
        url: "https://mapcore-bucket1.s3.us-west-2.amazonaws.com/hubmaps_heart/heart_m_metadata.json",
        format: "metadata",
      },
      viewer_2 : {
        url: "https://mapcore-bucket1.s3.us-west-2.amazonaws.com/hubmaps_heart/donor_with_view.json",
        format: "metadata",
      },
      gltfLink: "https://mapcore-bucket1.s3.us-west-2.amazonaws.com/hubmaps_heart/humanHeart_M.glb",
    };
  },
  methods: {
    setGLTFTransformation: function() {
      return() => {
        console.log("here")
        const childRegion = this.$refs.scaffold2.$module.scene.getRootRegion().getChildWithName("SPARCM");
        const group = childRegion.getGroup();
        group.position.set(0.04344825, -0.423371166, 0.0150267193);
        group.matrixAutoUpdate = true;
        const childArray = childRegion.findGeometriesWithGroupName("SPARCM_heart");
        childArray.forEach(object => {
            object.morph.matrixAutoUpdate = true;
            object.setAlpha(0.5);
          }
        )
        this.$refs.scaffold2.$module.scene.loadViewURL("https://mapcore-bucket1.s3.us-west-2.amazonaws.com/hubmaps_heart/heart_m_view.json");

        this.$refs.scaffold2.$module.unsetFinishDownloadCallback();
      }
    },
    onReady: function() {
      this.$refs.scaffold2.$module.setFinishDownloadCallback(
        this.setGLTFTransformation()
      );
      this.$refs.scaffold2.$module.loadGLTFFromURL(this.gltfLink, "scene", false);
      this.gltfLoaded = true;
    },
  },
};
</script>

<style>


#app {
  font-family: "Asap", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100%;
  width: 100%;
  position: absolute;
  overflow: hidden;
}

.left {
  height: 100%;
  width: 50%;
  border-right: 1px solid black;
  position: absolute;
  z-index:2;
}

.right {
  height: 100%;
  width: 50%;
  left:50%;
  position: absolute;
}

body {
  margin: 0px;
}

</style>
