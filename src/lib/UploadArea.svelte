<script>
	let image = null,
		filename;
	let showImage = false;
	let isLoading = false;
	let result;
	function setImage(e) {
		if (e.target) {
			filename = e.target.files[0];
			let reader = new FileReader();
			reader.readAsDataURL(filename);
			reader.onload = (e) => (image.src = e.target.result);
			showImage = true;
		}
	}
	async function predict() {
		let data = new FormData();
		data.append('file', filename);
		isLoading = true;
		const res = await fetch('https://skin-disease-ayqlhzi55a-el.a.run.app/predict', {
			method: 'POST',
			body: data
		});
		const json = await res.json();
		result = json.result;
		modal.showModal();
		isLoading = false;
	}
	function reset() {
		showImage = false;
		image = null;
		isLoading = false;
	}
</script>

<dialog id="modal">
	<article>
		<header>
			{#if result >= 0.5}
				<b>Safe!</b>
			{/if}
			{#if result < 0.5}
				<b>Warning!</b>
			{/if}
		</header>
		{#if result <= 0.5}
			Our model detects only Eczema, please consider getting this checked by a medical professional.
		{/if}
		{#if result > 0.5}
			Our model detects normal, healthy skin.
		{/if}
		<footer><button on:click={() => modal.close()}>Close</button></footer>
	</article>
</dialog>

<article>
	<header>Upload your image to get started!</header>
	<input accept="image/png, image/jpeg" type="file" name="file" id="file" on:change={setImage} />
	{#if showImage}
		<img alt="uploaded photo" bind:this={image} />
		<footer>
			<a href="#" role="button" aria-busy={isLoading} on:click|preventDefault={() => predict()}>
				Check this image
			</a>
			<a href="#" role="button" class="secondary" on:click={() => reset()}> Reset image </a>
		</footer>
	{/if}
</article>
