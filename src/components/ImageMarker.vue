<template>
	<div class="image-marker">
		<div class="image-marker__img">
			<img
				@click="handleAddMarker"
				src="https://i.pinimg.com/originals/bf/82/f6/bf82f6956a32819af48c2572243e8286.jpg"
				alt="Image to put marker on"
			/>
			<div
				:key="mi"
				class="image-marker__marker"
				:style="markerStyle(marker)"
				v-for="(marker, mi) in markers"
			>
				<div
					class="image-marker__circle"
					@click="handleDeleteMarker(marker.no)"
				></div>
				<div
					class="image-marker__text"
					contenteditable
					@input="handleInput($event, mi)"
				>
					{{ marker.text || marker.no }}
				</div>
			</div>
		</div>
	</div>
</template>

<script>
const localKey = "AMS-IMG-MARKERS"
export default {
	name: 'ImageMarker',
	data: () => ({
		markers: []
	}),
	created() {
		let markers = localStorage.getItem(localKey)
		if (markers) this.markers = JSON.parse(markers)
	},
	methods: {
		markerStyle({ top, left }) {
			return `
				top:${top}%;
				left:${left}%;
				transform:translate(-12px,-12px)
			`
		},
		handleInput(e, index) {
			this.markers[index].text = e.target.innerText
		},
		syncToLocalStorage() {
			localStorage.setItem(localKey, JSON.stringify(this.markers))
		},
		handleDeleteMarker(no) {
			this.markers = this.markers.filter(m => m.no !== no)
			this.syncToLocalStorage()
		},
		handleAddMarker(e) {
			let rect = e.target.getBoundingClientRect();

			// Calculating left and top in percentange
			let top = Math.round(((e.clientY - rect.top) / rect.height) * 100);
			let left = Math.round(((e.clientX - rect.left) / rect.width) * 100);

			// Maximum number
			let max = this.markers.length
				? this.markers
					.map(m => m.no)
					.sort((a, b) => a - b)
					.pop()
				: 0

			console.log({
				x: left,
				y: top
			})

			// Saving
			this.markers.push({ top, left, no: max + 1, text: null })
			this.syncToLocalStorage()
		}
	}
}
</script>

<style scoped>
	.image-marker {
		padding: 2rem;
		background-color: #313131;
		min-height: 100vh;
	}
	.image-marker__img {
		position: relative;
		overflow: hidden;
	}
	.image-marker__img img {
		width: 100%;
	}
	.image-marker__marker {
		position: absolute;
		text-align: center;
	}
	.image-marker__circle {
		width: 24px;
		height: 24px;
		border-radius: 50%;
		display: inline-block;
		background-color: transparent;
		border: 3px solid red;
		margin-bottom: 0.25rem;
	}
	.image-marker__text {
		font-size: 14px;
		color: white;
	}
</style>