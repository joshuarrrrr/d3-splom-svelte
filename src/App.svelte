<script>
  import ScatterplotMatrix from "./components/ScatterplotMatrix.svelte";
  import * as d3 from "d3";

  let data;
  let columns;
  let width = 500;

  d3.csv("/penguins.csv", d3.autoType).then(penguins => {
    data = penguins;
	columns = data.columns.filter(d => typeof data[0][d] === "number");
  });
</script>

<style>

</style>

<main>
  <section>
    <h1>Scatterplot Matrix</h1>
    <p>
      This scatterplot matrix implementation is pretty much based on <a href="https://observablehq.com/@d3/scatterplot-matrix"> the d3 example on observable</a>. It uses the <a href="https://allisonhorst.github.io/palmerpenguins/">palmer penguins dataset</a>.
    </p>
    <ScatterplotMatrix {data} {columns} />
  </section>
</main>
