<script lang="ts">
    import * as SC from "svelte-cubed";
    import type { Position } from "svelte-cubed/types/common";
    import * as THREE from "three";
    import Pendulum from "../lib/Pendulum.svelte";
    import concreteTextureColorPath from "../../static/Concrete030_2K_Color.jpg";
    import concreteTextureRoughnessPath from "../../static/Concrete030_2K_Roughness.jpg";
    import leatherTextureColorPath from "../../static/Leather027_2K_Color.jpg";
    import leatherTextureRoughnessPath from "../../static/Leather027_2K_Roughness.jpg";

    type Pendulum = {
        origin: Position;
        length: number;
        angle: number;
        phaseDiff: number;
        pointer: any;
    };

    const loader = new THREE.TextureLoader();
    let concreteTextureColor: THREE.Texture;
    let concreteTextureRoughness: THREE.Texture;
    let leatherTextureColor: THREE.Texture;
    let leatherTextureRoughness: THREE.Texture;

    let pendulums: Pendulum[] = [];

    async function spawnPendulums(): Promise<Boolean> {
        concreteTextureColor = await loader.loadAsync(concreteTextureColorPath);
        concreteTextureRoughness = await loader.loadAsync(concreteTextureRoughnessPath);
        leatherTextureColor = await loader.loadAsync(leatherTextureColorPath);
        leatherTextureRoughness = await loader.loadAsync(leatherTextureRoughnessPath);

        for (let i = 0; i < 12; i++) {
            pendulums.push({
                origin: [0, 3, i * 0.5],
                length: 2,
                angle: ((130 - i * 2) * Math.PI) / 180,
                phaseDiff: i,
                pointer: null,
            });
        }

        return true;
    }

    SC.onFrame(() => {
        // Loop through pendulums and call the update method for each one.
        pendulums.forEach((p) => {
            if (p.pointer) {
                p.pointer.update();
            }
        });
    });
</script>

<div class="">
    <h1 class="text-8xl font-extrabold text-white text-center">Pendulums</h1>

    <p class="text-white font-semibold text-3xl mt-4 text-center">
        Pendulum movement using SvelteKit, Svelte Cubed and ThreeJS.
    </p>
</div>

<div class="grid place-items-center mt-8">
    <div class="demo">
        <SC.Canvas
            antialias
            shadows
            background={new THREE.Color("papayawhip")}
            fog={new THREE.FogExp2("papayawhip", 0.05)}
        >
            <SC.PerspectiveCamera position={[0, 3, -3]} target={[0, 0, 5]} />
            <SC.OrbitControls maxPolarAngle={Math.PI * 0.51} />
            <SC.AmbientLight intensity={0.6} />
            <SC.DirectionalLight
                intensity={0.6}
                position={[0, 10, -10]}
                shadow={{ mapSize: [2048, 2048] }}
            />

            <SC.Group position={[0, 0, 0]}>
                <SC.Mesh
                    geometry={new THREE.PlaneGeometry(50, 50)}
                    material={new THREE.MeshStandardMaterial({ color: "burlywood" })}
                    rotation={[-Math.PI / 2, 0, 0]}
                    receiveShadow
                />

                <SC.Primitive object={new THREE.GridHelper(50, 50, "papayawhip", "papayawhip")} />
            </SC.Group>

            {#await spawnPendulums()}
                <div />
            {:then loaded}
                {#each pendulums as pendulum}
                    <Pendulum
                        bind:this={pendulum.pointer}
                        origin={pendulum.origin}
                        angle={pendulum.angle}
                        ballTextureColor={concreteTextureColor}
                        ballTextureRoughness={concreteTextureRoughness}
                        cableTextureColor={leatherTextureColor}
                        cableTextureRoughness={leatherTextureRoughness}
                    />
                {/each}
            {/await}
        </SC.Canvas>
    </div>
</div>

<div class="mt-20 text-xl text-white text-center">
    <p>Created by Henry Rocha.</p>
</div>

<style lang="postcss">
    .demo {
        @apply relative;
        width: 800px;
        height: 600px;
    }
</style>
