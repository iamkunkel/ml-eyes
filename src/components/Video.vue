<template>
  <div class="video"> 
    <video
      autoplay
      muted
      playsInline
      v-bind="cameraFeed"
      id="video-feed"
    />
    <img src="@/assets/eyelid.svg">
  </div>
</template>

<script>
import '@tensorflow/tfjs';
import * as cocoSsd from '@tensorflow-models/coco-ssd';
import { reactive, onMounted } from 'vue';
import webNotification from 'simple-web-notification';
import logo from '@/assets/logo_noti.png'

export default {
  name: 'App',
  setup(){
    const cameraFeed = reactive({
      srcObject: null,
    });
		
    const constraint = {
      audio: false,
      video: true
    }
    
    // Loads the webcam
    const webcam = async() => {
      try {
        cameraFeed.srcObject = await navigator.mediaDevices.getUserMedia(constraint);
        await cameraFeed.onloadedmetadata;
      }
      catch(e) {
        console.log(e);
      }
    }

    // Loads the model
    const load = async() => {
      try {
        await webcam();
        const loadCocoModel = await cocoSsd.load({
					base:"mobilenet_v1"
				});
        const feed = document.getElementById('video-feed');
        detectFromVideoFrame(loadCocoModel, feed);
      }
      catch(e){
        console.log(e);
      }
    }

    const notify = () => {
      webNotification.showNotification('Eyes Behind', {
      body: 'There is a person approaching',
      icon: logo,
      autoClose: 8000
      })
    }
    
    const detectPeople = (predictions) => {
      let totalPeople = 0;
      for(let i = 0; i < predictions.length; i++) {
        if(predictions[i].class == 'person') {
          if(predictions[i].score >= .7){
            totalPeople++;
            if(totalPeople >= 2){
              notify();
            }
          }
        }
      }
    }

    // the heart of the app. Runs the model across each frame
    const detectFromVideoFrame = (model, video) => {
      model.detect(video).then(predictions => {
        console.log(predictions)
        detectPeople(predictions)
        requestAnimationFrame(() => {
          detectFromVideoFrame(model, video);
        });
      }, 
      (error) => {
        console.log(error);
      });
    }

    onMounted(() => {
      if(navigator.mediaDevices.getUserMedia || navigator.mediaDevices.webkitGetUserMedia) {
        load();
      } 
      else {
        alert("Not supported.");
      }
    });

    return {
      cameraFeed,
    }
  }
}
</script>

<style scoped>

#video-feed {
  position: fixed;
  width: 35vw;
  height: 30vw;
  top: 50%;
  left: 50%;
  max-height: 400px;
  max-width: 400px;
  transform: translate(-50%, -50%);
  position:fixed;
  object-fit: cover;
  -webkit-mask-box-image: url('~@/assets/pupil.svg');
}

img {
  position: fixed;
  width: 70vw;
  height: 55vw;
  top: 50%;
  left: 50%;
  max-height: 400px;
  transform: translate(-50%, -50%);
  position:fixed;
}
</style>