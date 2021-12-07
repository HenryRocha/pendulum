<script lang="ts">
    import * as THREE from "three";
    import * as SC from "svelte-cubed";
    import Pendulum from "../lib/Pendulum.svelte";

    let pendulums = [];
    for (let i = 0; i < 10; i++) {
        let origin = [0, 0, i * 0.8];
        let theta = -90 + i * 0.05;
        pendulums.push({
            origin,
            theta,
            obj: null,
        });
    }

    SC.onFrame(() => {
        // Loop through pendulums and call the update method
        // for their objects.
        pendulums.forEach((p) => {
            if (p.obj) {
                p.obj.update();
            }
        });
    });
</script>

<SC.Canvas antialias shadows>
    <SC.PerspectiveCamera position={[0, 2, -5]} target={[0, 0, 0]} />
    <SC.OrbitControls />
    <SC.AmbientLight intensity={0.6} />

    <SC.Group position={[0, 0, 0]}>
        <SC.Primitive object={new THREE.GridHelper(50, 50, "papayawhip", "papayawhip")} />
    </SC.Group>

    {#each pendulums as pendulum}
        <Pendulum bind:this={pendulum.obj} origin={pendulum.origin} theta={pendulum.theta} />
    {/each}
</SC.Canvas>
