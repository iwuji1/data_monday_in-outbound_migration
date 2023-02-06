<script>
    export let data;
</script>

<style>

h1 {
  font-family: Avenir,system-ui,-apple-system,Helvetica,Arial,sans-serif;
  font-weight: 600;
}

h3 {
  font-weight: 400;
  line-height: 24px;
  margin: 0;
}

.tooltip {
  position:absolute;
  /* background: white; */
  box-shadow: rgba(0, 0, 0, 0.15) 2px 3px 8px; /* Gives a nice 3d effect */
  padding: 8px 6px; /* Adds space around content within tooltip */
  border-radius: 3px; /* Rounds corners */
  pointer-events: none
}

</style>

<div class="tooltip"
  style="position: absolute;
  top:600px;
  left:900px">
  <h1>{data.State}</h1>
  <h3>Inbound movement: {Math.round(data.Inbound*100)}%</h3>
  <h3>Outbound movement: {Math.round(data.Outbound*100)}%</h3>
</div>
