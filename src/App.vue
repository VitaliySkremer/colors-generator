<template>
	<ColumnColor 
    v-for="item in columnsColor" 
    :key="item.id"
    :column="item"
		@toggleLock="toggleLock"
    />
		<button @click="setRandomColors" v-if="isMobile" class="btn__update">
			<svg fill="#000000" viewBox="0 0 24 24" id="update-alt" data-name="Flat Line" xmlns="http://www.w3.org/2000/svg" class="icon flat-line"><path id="primary" d="M5.07,8A8,8,0,0,1,20,12" style="fill: none; stroke: rgb(0, 0, 0); stroke-linecap: round; stroke-linejoin: round; stroke-width: 2;"></path><path id="primary-2" data-name="primary" d="M18.93,16A8,8,0,0,1,4,12" style="fill: none; stroke: rgb(0, 0, 0); stroke-linecap: round; stroke-linejoin: round; stroke-width: 2;"></path><polyline id="primary-3" data-name="primary" points="5 3 5 8 10 8" style="fill: none; stroke: rgb(0, 0, 0); stroke-linecap: round; stroke-linejoin: round; stroke-width: 2;"></polyline><polyline id="primary-4" data-name="primary" points="19 21 19 16 14 16" style="fill: none; stroke: rgb(0, 0, 0); stroke-linecap: round; stroke-linejoin: round; stroke-width: 2;"></polyline></svg>
		</button>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import ColumnColor from './components/ColumnColor.vue'
import chroma from "chroma-js";

export interface IColor {
	id:number;
	color: string;
	lock: boolean;
}

let columnsColor = ref<IColor[]>([
	{id:1,color:'white', lock:false},
	{id:2,color:'white', lock:false},
	{id:3,color:'white', lock:false},
	{id:4,color:'white', lock:false},
	{id:5,color:'white', lock:false},
])

const setRandomColors = () =>{
	const colors:string[] = [];
	columnsColor.value = columnsColor.value.map(item=>{
		if(!item.lock){
			const randomColor = chroma.random();
			colors.push(randomColor)
			return {...item, color: randomColor}
		}
		colors.push(item.color);
		return item
	})

	setHashColors(colors)
}

const setHashColors = (colors:string[] = []) =>{
	document.location.hash = colors.map(color=> color.toString().substring(1)).join('-')
}

const toggleLock = (id:number)=>{
	columnsColor.value = columnsColor.value.map(item=>{
		if(item.id === id){
			return {...item, lock: !item.lock}
		}
		return item;
	})
}

const initColumnColors = () =>{
	const arrHashColors = document.location.hash.substring(1).split('-').map(color=> `#${color}`)
	for(let i =0; i<arrHashColors.length; i++){
		columnsColor.value[i].color = arrHashColors[i];
	}
}

const isMobile = ref(false);

onMounted(() => {
	if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
    // код для мобильных устройств
		isMobile.value = true;
  }

	if(document.location.hash.length>1){
		initColumnColors()
	}
	else {
		setRandomColors();
	}

	document.addEventListener('keydown',(event)=>{
		event.preventDefault();
		if(event.code.toLocaleLowerCase() === 'space'){
			setRandomColors();
		}
	})
})

</script>
<style lang='scss' scoped>

.btn__update {
	position: fixed;
	left: calc(var(--index)*1.5);
	top: calc(var(--index)*1.5);
	width: calc(var(--index)*10);
	height: calc(var(--index) * 10);
	border-radius: 50%;
	background-color: transparent;
	border: none;
	background-color: rgba(0,0,0,.3);

	svg {
		width: calc(var(--index)*5);
		height: calc(var(--index) * 5);
	}
}

</style>
