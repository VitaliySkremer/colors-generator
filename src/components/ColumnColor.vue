<template>
	<div class="col" :style="{ '--color': props.column.color, '--color-text': textColor }">
		<button
			@click="copyColor"
			class="col_title"
		>
			{{ props.column.color }}
		</button>
		<p :class="{'col_copy':true,'active':isCopy}">цвет скопирован</p>
		<button @click="handleClick" class="col_button">
			<svg
				v-if="props.column.lock"
				width="30px"
				height="30px"
				viewBox="0 0 24 24"
				fill="none"
				xmlns="http://www.w3.org/2000/svg"
			>
				<path
					fill-rule="evenodd"
					clip-rule="evenodd"
					d="M8.00001 5V8.00415C7.50208 8.01124 7.05704 8.03041 6.67222 8.08214C6.0167 8.17028 5.38835 8.36902 4.87869 8.87868C4.36902 9.38835 4.17028 10.0167 4.08215 10.6722C3.99991 11.2839 3.99995 12.0477 4 12.9342V17.0658C3.99995 17.9523 3.99991 18.7161 4.08215 19.3278C4.17028 19.9833 4.36902 20.6117 4.87869 21.1213C5.38835 21.631 6.0167 21.8297 6.67222 21.9179C7.28387 22.0001 8.0477 22.0001 8.93417 22H15.0659C15.9523 22.0001 16.7161 22.0001 17.3278 21.9179C17.9833 21.8297 18.6117 21.631 19.1213 21.1213C19.631 20.6117 19.8297 19.9833 19.9179 19.3278C20.0001 18.7161 20.0001 17.9523 20 17.0658V12.9342C20.0001 12.0477 20.0001 11.2839 19.9179 10.6722C19.8297 10.0167 19.631 9.38835 19.1213 8.87868C18.6117 8.36902 17.9833 8.17028 17.3278 8.08215C16.943 8.03041 16.4979 8.01124 16 8.00415V5C16 3.34315 14.6569 2 13 2H11C9.34316 2 8.00001 3.34315 8.00001 5ZM11 4C10.4477 4 10 4.44772 10 5V8L14 8V5C14 4.44772 13.5523 4 13 4H11ZM12 13C10.8954 13 10 13.8954 10 15C10 16.1046 10.8954 17 12 17C13.1046 17 14 16.1046 14 15C14 13.8954 13.1046 13 12 13Z"
					:fill="textColor"
				/>
			</svg>

			<svg
				v-else
				width="30px"
				height="30px"
				viewBox="0 0 24 24"
				fill="none"
				xmlns="http://www.w3.org/2000/svg"
			>
				<path
					fill-rule="evenodd"
					clip-rule="evenodd"
					d="M11 4C10.4477 4 10 4.44772 10 5V8H15.0658C15.9523 7.99995 16.7161 7.99991 17.3278 8.08215C17.9833 8.17028 18.6117 8.36902 19.1213 8.87868C19.631 9.38835 19.8297 10.0167 19.9179 10.6722C20.0001 11.2839 20.0001 12.0477 20 12.9342V17.0658C20.0001 17.9523 20.0001 18.7161 19.9179 19.3278C19.8297 19.9833 19.631 20.6117 19.1213 21.1213C18.6117 21.631 17.9833 21.8297 17.3278 21.9179C16.7161 22.0001 15.9523 22.0001 15.0658 22H8.93414C8.04767 22.0001 7.28387 22.0001 6.67222 21.9179C6.0167 21.8297 5.38835 21.631 4.87869 21.1213C4.36902 20.6117 4.17028 19.9833 4.08215 19.3278C3.99991 18.7161 3.99995 17.9523 4 17.0658V12.9342C3.99995 12.0477 3.99991 11.2839 4.08215 10.6722C4.17028 10.0167 4.36902 9.38835 4.87869 8.87868C5.38835 8.36902 6.0167 8.17028 6.67222 8.08214C7.05704 8.03041 7.50208 8.01124 8.00001 8.00415V5C8.00001 3.34315 9.34316 2 11 2H13.0625C14.6848 2 16 3.31516 16 4.9375V5C16 5.55228 15.5523 6 15 6C14.4477 6 14 5.55228 14 5V4.9375C14 4.41973 13.5803 4 13.0625 4H11ZM12 13C10.8954 13 10 13.8954 10 15C10 16.1046 10.8954 17 12 17C13.1046 17 14 16.1046 14 15C14 13.8954 13.1046 13 12 13Z"
					:fill="textColor"
				/>
			</svg>
		</button>
	</div>
</template>

<script setup lang="ts">
import chroma from 'chroma-js'
import { computed, ref } from 'vue'
import { IColor } from '../App.vue'

const props = defineProps<{
	column: IColor
}>()

let isCopy = ref(false)

const emmit = defineEmits<{
	(e: 'toggleLock', id: number): void
}>()

const textColor = computed(() => {
	return chroma(props.column.color).luminance() > 0.5 ? '#333' : '#fefefe'
})

const handleClick = () => {
	emmit('toggleLock', props.column.id)
}

const copyColor = () => {
	navigator.clipboard.writeText(props.column.color)
	isCopy.value = true;
	setTimeout(()=>{
		isCopy.value = false;
	}, 2_000)
}
</script>

<style lang="scss" scoped>
.col {
	--color: #fefefe;
	--color-text: white;
	position: relative;
	flex: 1;
	display: flex;
	align-items: center;
	justify-content: space-around;
	flex-direction: column;
	background-color: var(--color);

	&_title {
		color: var(--color-text);
		font-size: calc(var(--index));
		letter-spacing: 0.1em;
		cursor: pointer;
		padding: calc(var(--index)/2);
		border-radius: 8px;
		background-color: transparent;
		border: none;
		transition: background-color 0.2s ease;

		&:hover {
			background-color: rgba(0, 0, 0, 0.2);
		}

		&:active {
			background-color: rgba(0, 0, 0, 0.3);
		}
	}

	&_copy {
		color: var(--color-text);
		font-size: calc(var(--index));
		padding: calc(var(--index)/2);
		border-radius: 8px;
		border: 2px solid var(--color-text);
		position: absolute;
		transition: opacity .2s ease;
		opacity: 0;
		transform: scale(0);
	}

	&_button {
		background-color: transparent;
		border: none;
		color: black;
		cursor: pointer;
		outline: none;
		padding: calc(var(--index)/4);
		transition: background-color 0.2s ease;
		border-radius: 8px;

		&:hover {
			background-color: rgba(0, 0, 0, 0.2);
		}
	}
}

.active {
	opacity: 1;
	transform: scale(1);
}


@media all and (max-width:600px){
  .col{
		flex-direction: row;
	}
}
</style>
