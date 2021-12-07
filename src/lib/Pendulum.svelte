<script lang="ts">
    import * as SC from "svelte-cubed";
    import type { Position } from "svelte-cubed/types/common";
    import * as THREE from "three";

    export let origin: Position = [0, 0, 0];
    export let length: number = 2;
    export let angle: number = (90 * Math.PI) / 180;

    export let ballTextureColor: THREE.Texture;
    export let ballTextureRoughness: THREE.Texture;
    export let cableTextureColor: THREE.Texture;
    export let cableTextureRoughness: THREE.Texture;

    let angleAcceleration: number = 0;
    let angleVelocity: number = 0;
    let phaseDiff: number = 0.00001;

    let ball: any;
    let ballPosX: number = origin[0];
    let ballPosY: number = origin[1];
    let ballPosZ: number = origin[2];

    let cable: any;
    let cablePosX: number = origin[0];
    let cablePosY: number = origin[1];
    let cablePosZ: number = origin[2];

    const ballGeometry = new THREE.SphereGeometry();
    const ballMaterial = new THREE.MeshStandardMaterial({
        map: ballTextureColor,
        roughness: 1,
        roughnessMap: ballTextureRoughness,
        metalness: 0.2,
    });
    ball = new THREE.Mesh(ballGeometry, ballMaterial);

    const cableGeometry = new THREE.CylinderGeometry(0.01, 0.01, length);
    const cableMaterial = new THREE.MeshStandardMaterial({
        map: cableTextureColor,
        roughness: 1,
        roughnessMap: cableTextureRoughness,
        metalness: 0.2,
    });
    cable = new THREE.Mesh(cableGeometry, cableMaterial);
    cable.geometry.translate(0, length / 2, 0);

    function updateAngles() {
        angleAcceleration = 2 * (0.0001 + phaseDiff) * Math.sin(angle);
        angleVelocity += angleAcceleration;
        angle += angleVelocity;
    }

    function updatePosition() {
        ballPosX = origin[0] + length * Math.sin(angle);
        ballPosY = origin[1] + length * Math.cos(angle);
        ballPosZ = origin[2];
    }

    export function update() {
        updateAngles();
        updatePosition();
    }
</script>

<SC.Mesh
    geometry={cable.geometry}
    material={cable.material}
    position={[cablePosX, cablePosY, cablePosZ]}
    rotation={[0, 0, -angle]}
    castShadow
/>

<SC.Mesh
    geometry={ball.geometry}
    material={ball.material}
    position={[ballPosX, ballPosY, ballPosZ]}
    rotation={[0, 0, -angle]}
    scale={[0.2, 0.2, 0.2]}
    castShadow
/>
