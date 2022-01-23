<script>
	import { onMount } from "svelte";
	let pagination = {};
	let movies = [];
	let pages = 1;

	onMount(async function getMovies() {
		const urlParams = new URLSearchParams(window.location.search);
		const currentPage = urlParams.get("page");
		if (!currentPage || currentPage < 1) {
			window.location.replace("/?page=1");
		}
		let skip = 0;
		if (currentPage > 0) {
			skip = (currentPage - 1) * 10;
		}
		const res = await fetch(
			"http://localhost:3000/movies?take=10&skip=" + skip
		);
		const json = await res.json();
		movies = json.data;
		pagination = json.pagination;
		pages = Math.ceil(pagination.count / pagination.take);
	});

	function gotoPage(page) {
		window.location.replace("/?page=" + page);
	}
</script>

<main>
	<div class="d-flex flex-wrap justify-content-evenly">
		{#each movies as movie}
			<div class="mb-3">
				<div class="card m-0" style="width: 20rem;height: 740px;">
					<h5 class="card-header">Released on : {movie.year}</h5>
					<img
						style="height:400px;width:400px;object-fit: cover;"
						src={movie.poster}
						class="card-img-top img-thumbnail rounded mx-auto d-block"
						alt={movie.title}
					/>
					<div class="card-body">
						<h5 class="card-title">
							{movie.title}
						</h5>
						<p class="card-text">
							{movie.fullplot.slice(0, 250) + "..."}
						</p>
					</div>
					<div class="card-footer text-muted">
						Genres :
						{#each movie.genres as genre, i}
							{#if i != movie.genres.length - 1}
								{genre},&nbsp;
							{:else}
								{genre}.
							{/if}
						{/each}
					</div>
				</div>
			</div>
		{/each}
	</div>
	<div class="d-flex justify-content-center mt-5">
		<nav aria-label="Page navigation example">
			<ul class="pagination">
				{#if pages > 1 && new URLSearchParams(window.location.search).get("page") != 1}
					<li class="page-item">
						<a
							class="page-link"
							href="#/"
							on:click|preventDefault={() => {
								gotoPage(
									new URLSearchParams(
										window.location.search
									).get("page") - 1
								);
							}}
							aria-label="Previous"
						>
							<span aria-hidden="true">&laquo;</span>
						</a>
					</li>
				{/if}
				{#each { length: pages } as page, i}
					{#if i + 1 == new URLSearchParams(window.location.search).get("page")}
						<li class="page-item active">
							<a
								class="page-link active"
								href="#/"
								on:click|preventDefault={() => {
									gotoPage(i + 1);
								}}>{i + 1}</a
							>
						</li>
					{:else}
						<li class="page-item">
							<a
								class="page-link"
								href="#/"
								on:click|preventDefault={() => {
									gotoPage(i + 1);
								}}>{i + 1}</a
							>
						</li>
					{/if}
				{/each}
				{#if pages > 1 && new URLSearchParams(window.location.search).get("page") != pages}
					<li class="page-item">
						<a
							class="page-link"
							href="#/"
							on:click|preventDefault={() => {
								gotoPage(
									parseInt(
										new URLSearchParams(
											window.location.search
										).get("page")
									) + 1
								);
							}}
							aria-label="Next"
						>
							<span aria-hidden="true">&raquo;</span>
						</a>
					</li>
				{/if}
			</ul>
		</nav>
	</div>
</main>
