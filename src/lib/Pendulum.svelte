<script lang="ts">
    import * as THREE from "three";
    import * as SC from "svelte-cubed";
    import type { Position } from "svelte-cubed/types/common";

    export let origin: Position = [0, 0, 0];
    export let length: number = 1;
    export let color: number = 0xff0000;

    export let theta: number = -90;
    let angularAccelation: number = 0;
    let angularVelocity: number = 0;

    const ballGeometry = new THREE.BoxGeometry();
    const ballMaterial = new THREE.MeshStandardMaterial({ color: color });
    const ball = new THREE.Mesh(ballGeometry, ballMaterial);
    const ballPosition: Position = [origin[0], origin[1] - length, origin[2]];

    const cableGeometry = new THREE.CylinderGeometry(0.01, 0.01, length);
    const cableMaterial = new THREE.MeshStandardMaterial({ color: color });
    const cable = new THREE.Mesh(cableGeometry, cableMaterial);
    const cablePosition: Position = [origin[0], origin[1] - length / 2, origin[2]];

    export function update() {
        angularAccelation = -0.001 * Math.sin(theta);
        theta += angularVelocity;
        angularVelocity += angularAccelation;
        angularVelocity *= 0.999;
    }
</script>

<SC.Group position={[0, 0, 0]} rotation={[0, 0, theta]}>
    <SC.Mesh geometry={cable.geometry} material={cable.material} position={cablePosition} />

    <SC.Mesh
        geometry={ball.geometry}
        material={ball.material}
        position={ballPosition}
        scale={[0.5, 0.5, 0.5]}
    />
</SC.Group>
