---
title: "1分でミニマルなThree.js環境をつくる（TypeScript + React)"
emoji: "🖥️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["threejs", "React", "WebGL", "TypeScript"]
published: false
---

## モチベーション  

three.jsをサクッと書き始める最小構成を作る方法をまとめました。
もちろん他の方法も色々あると思いますが、この方法は1分でできて、そこそこニーズを満たせて、自由に拡張しやすいです。

ゴールは「**Sceneを作ってBoxを置いて簡単なライトで照らすぐらいまで**」とします。

*ゴールのイメージ*
![](/images/minimal-three-js-setup/red-box-rotate.gif)

️
☘️ こんな人にオススメ
- とりあえずさっさとthree.jsを書き始めたい
- メンテされてなさそうなあんまりよく分からないテンプレートは使いたくない
- ホットリロードとかほしい
- Textureなどをロードするのにdevサーバーは必要
  - Next.jsなどは若干やりすぎで、SSRやキャッシュ周りでいらない穴にハマりやすい
- TypeScriptのサポートはほしい
- おまけ: Reactで書きたい

Reactを使う場合と、素のJSの場合を用意しました。

## 実装

### React版  

1. 純粋SPAなReact + TS環境を作成

```bash
npm create vite@latest {好きなプロジェクト名} -- --template react-ts
```

2. 作ったプロジェクトに `cd` する

3. `npm install`

4. three.jsとR3F(react-three-fiber)をinstall

```bash
npm install three @react-three/fiber
```

```bash
npm install -D @types/three 
```

5. （お好みで）生成されたファイルを整理


`app.css` は削除し、`index.css` を↓ぐらいに書き換える（お好みで）
```css:index.css
body {
  margin: 0;
  padding: 0;
}
```

6. `App.tsx` を書き換え


```typescript:App.tsx
import { Canvas } from "@react-three/fiber";
function App() {
  return (
    <Canvas style={{ width: "100vw", height: "100vh" }}>
      <ambientLight intensity={Math.PI / 2} />
      <mesh>
        <boxGeometry args={[1, 1, 1]} />
        <meshStandardMaterial color="red" />
      </mesh>
    </Canvas>
  );
}

export default App;
```

これで一応完成🎉

![](/images/minimal-three-js-setup/red-box.png)

7. おまけ: マウス操作をつける

`OrbitControls` がある方が便利、という場合は

```bash
npm install @react-three/drei
```

をして、

```typescript:App.tsx
import { OrbitControls } from "@react-three/drei";
// 省略
function App() {
  return (
    <Canvas style={{ width: "100vw", height: "100vh" }}>
      // 省略
      <OrbitControls /> // ← 追加
    </Canvas>
  );
}
```

これで、マウス操作でのズームと回転がつく


![](/images/minimal-three-js-setup/red-box-rotate.gif)


### Vanilla JS版  

React版とほぼ同じような手順で進めていきます。

1. Viteで環境を作成

```bash
npm create vite@latest {好きなプロジェクト名} -- --template vanilla-ts
```

2. 作ったプロジェクトに `cd` する

3. `npm install`

4. three.jsをinstall

```bash
npm install three
```

```bash
npm install -D @types/three
```

5. （お好みで）生成されたファイルを整理

`style.css` を↓ぐらいに書き換える（お好みで）
```css:style.css
body {
  margin: 0;
  padding: 0;
}
```

6. `main.ts` を書き換え

```typescript:main.ts
import * as THREE from 'three';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 5;

const renderer = new THREE.WebGLRenderer();
renderer.setClearColor(0xffffff);
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

scene.add(new THREE.AmbientLight(0xffffff, Math.PI / 2));

const cube = new THREE.Mesh(
  new THREE.BoxGeometry(1, 1, 1),
  new THREE.MeshStandardMaterial({ color: 'red' })
);
scene.add(cube);

renderer.render(scene, camera);
```

これで完成🎉

![](/images/minimal-three-js-setup/red-box.png)

7. おまけ: マウス操作をつける

```typescript:main.ts
import * as THREE from 'three';
// @ts-ignore
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

//省略

const controls = new OrbitControls(camera, renderer.domElement);

function animate() {
  requestAnimationFrame(animate);
  controls.update();
  renderer.render(scene, camera);
}

animate();
```

これで、マウス操作でのズームと回転がつきます

![](/images/minimal-three-js-setup/red-box-rotate.gif)


## まとめ

（本当に1分で出来るかどうかはさておき）結構さっさと書き始められて快適です🎉

とりあえずViteが時代遅れになるまでは、これで特にスタートして大きな問題はないかと思います。
