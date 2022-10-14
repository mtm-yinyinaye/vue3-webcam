<template>
  <div class="home">
    <div class="content">
      <img
        v-if="!isOpen"
        :src="camera_icon"
        alt="Camera"
        class="camera_icon"
        @click="openCamera"
      />
      <div v-else>
        <Camera :resolution="{ width: 375, height: 812 }" ref="camera" autoplay>
          <div class="camera_frame">
            <div class="input-file-body">
              <input
                type="file"
                name="uploadFile"
                accept="png, .jpg, .jpeg, .mp4, .mov"
                ref="file"
                class="input-file"
                @change="getUploadImage"
              />
              <img :src="gallery_img" class="gallery_img" />
            </div>
            <img
              :src="camera_img"
              alt="Camera"
              class="camera_img"
              @click="snapshot"
            />
            <div>
              <img
                v-if="recording"
                :src="record_img"
                alt="record_img"
                class="record_img"
                @click="stopRecord"
              />
              <img
                v-else
                :src="start_record_img"
                alt="start_record_img"
                class="record_img"
                @click="startRecord"
              />
            </div>
          </div>
        </Camera>
      </div>
      <img v-if="imagePreview && !isOpen" class="image_preview" :src="imagePreview" />
      <div v-if="videopreview && !isOpen">
        <video :src="videopreview" width="360" height="360" controls></video>
        <a :href="download_link" id="download-video" download="test.mp4">Download Video</a>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import Camera from "simple-vue-camera";
import camera_icon from "@/assets/img/camera-icon.png";
import camera_img from "@/assets/img/camera.png";
import gallery_img from "@/assets/img/gallery.png";
import record_img from "@/assets/img/stop_record.jpg";
import start_record_img from "@/assets/img/player_record.png";

//data
const camera = ref<InstanceType<typeof Camera>>();
const isOpen = ref(false);
const imagePreview = ref<File | null>() as any;
const videopreview = ref<any | null>();
const recording = ref(false);
const download_link = ref<any>();
let media_recorder: any = null;
let blobs_recorded: any = [];

// method
const openCamera = () => {
  isOpen.value = true;
};

const snapshot = async () => {
  const blob: any = await camera.value?.snapshot();
  const url = URL.createObjectURL(blob);
  imagePreview.value = url;
  isOpen.value = false;
};

const getUploadImage = (e: Event) => {
  const target = e.target as HTMLInputElement;
  if (target != null && target.files != null) {
    setNullData();
    const file = target.files[0];
    const fileName = file.name;
    const ext = fileName.substring(fileName.lastIndexOf(".") + 1).toLowerCase();
    let blobURL = URL.createObjectURL(file);
    const imageFileType = ext == "png" || ext == "jpg" || ext == "jpeg";
    imageFileType
      ? (imagePreview.value = blobURL)
      : (videopreview.value = blobURL);
    isOpen.value = false;
  }
};

const startRecord = () => {
  navigator.mediaDevices
    .getUserMedia({
      video: true,
      audio: true,
    })
    .then((stream) => {
      media_recorder = new MediaRecorder(stream, {
        mimeType: "video/webm",
      });
      media_recorder.start();
      recording.value = true;
      media_recorder.ondataavailable = (e: any) => {
        blobs_recorded.push(e.data);
      };
      media_recorder.onstop = () => {
        let recordedBlob = new Blob(blobs_recorded, { type: "video/mp4" });
        videopreview.value = URL.createObjectURL(recordedBlob);
        download_link.value = videopreview.value;
        isOpen.value = false;
        stream.getTracks().forEach((track: any) => track.stop());
      };
    });
};
const stopRecord = () => {
  setNullData();
  media_recorder.stop();
};

const setNullData = () => {
  imagePreview.value = null;
  videopreview.value = null;
}
</script>

<style scoped>
.camera_icon {
  width: 50px;
  height: 50px;
  cursor: pointer;
}
.content {
  width: 300px;
  margin: 0 auto;
}
.image_preview {
  width: 300px;
  max-height: 80vh;
  margin: 0 auto;
}
.camera_frame {
  position: absolute;
  bottom: 0;
  background: #000;
  width: 100%;
  height: 50px;
}
.camera_img {
  position: absolute;
  bottom: 15px;
  right: 15px;
  width: 30px;
  height: 25px;
  cursor: pointer;
}
.input-file {
  -webkit-appearance: none;
  appearance: none;
  position: absolute;
  z-index: 2;
  bottom: 5px;
  left: 15px;
  opacity: 0;
  width: 40px;
  height: 100%;
  cursor: pointer;
}
.gallery_img {
  position: absolute;
  bottom: 5px;
  left: 15px;
  width: 40px;
  height: 40px;
  cursor: pointer;
}
.close video,
.show.class video {
  width: 0;
  height: 0;
}
.record_img {
  width: 30px;
  position: absolute;
  bottom: 10px;
}
</style>
