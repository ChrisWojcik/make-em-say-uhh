<script context="module">
	export const prerender = true;
</script>

<script>
	import Counter from '$lib/Counter.svelte';
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>
	<h1>
		<div class="welcome">
			<picture>
				<source srcset="svelte-welcome.webp" type="image/webp" />
				<img src="svelte-welcome.png" alt="Welcome" />
			</picture>
		</div>

		to your new<br />SvelteKit app
	</h1>

	<h2>
		try editing <strong>src/routes/index.svelte</strong>
	</h2>

	<div style="width: 100%; height: 0; position: relative; padding-bottom: 56.25%;">
		<video
			style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"
			playsinline
			controls
		/>
	</div>
	<script>
		var video = document.querySelector('video');

		var assetURLs = ['clip_1.mp4', 'clip_2.mp4', 'clip_3.mp4'];
		var mimeCodec = 'video/mp4; codecs="avc1.640028, mp4a.40.2"';

		if ('MediaSource' in window && MediaSource.isTypeSupported(mimeCodec)) {
			var mediaSource = new MediaSource();

			video.src = URL.createObjectURL(mediaSource);
			mediaSource.addEventListener('sourceopen', sourceOpen);
		} else {
			console.error('Unsupported MIME type or codec: ', mimeCodec);
		}

		function sourceOpen(_) {
			var mediaSource = this;
			var sourceBuffer = mediaSource.addSourceBuffer(mimeCodec);
			sourceBuffer.mode = 'sequence';

			Promise.all(
				assetURLs.map(function (assetURL) {
					return fetchAB(assetURL);
				})
			)
				.then(function (bufs) {
					let i = 0;

					sourceBuffer.addEventListener('updateend', function (e) {
						if (!sourceBuffer.updating && mediaSource.readyState === 'open') {
							if (i < bufs.length - 1) {
								i++;
								sourceBuffer.appendBuffer(bufs[i]);
							} else {
								mediaSource.endOfStream();
							}
						}
					});

					sourceBuffer.appendBuffer(bufs[i]);
				})
				.catch(function (err) {
					console.error(err);
				});
		}

		function fetchAB(url) {
			return new Promise(function (resolve, reject) {
				var xhr = new XMLHttpRequest();
				xhr.open('get', url);
				xhr.responseType = 'arraybuffer';
				xhr.onload = function () {
					resolve(xhr.response);
				};
				xhr.onerror = function () {
					reject(new Error());
				};
				xhr.send();
			});
		}
	</script>
	<Counter />
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 1;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
</style>
