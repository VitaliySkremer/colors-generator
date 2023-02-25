<template>
	<ColumnColor 
    v-for="item in columnsColor" 
    :key="item.id"
    :column="item"
		@toggleLock="toggleLock"
    />
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

onMounted(() => {
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
<style scoped></style>
