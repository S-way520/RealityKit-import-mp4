# RealityKit-import-mp4
RealityKit and SwiftUI 
# The first part
![IMG_0158](https://github.com/S-way520/RealityKit-import-mp4/assets/95877651/b8c45df9-1d1e-4672-a0c8-4dddd3902823)
# The second part
Code file 1
```swift
import SwiftUI
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```
Code file 2
```swift
import SwiftUI
import RealityKit
import AVFoundation
struct ContentView: View {
    var body: some View {
        return ARViewContainer().edgesIgnoringSafeArea(.all)
    }
}
struct ARViewContainer: UIViewRepresentable {
    func makeUIView(context: Context) -> ARView {
        let arView = ARView(frame: .zero)
        let anchor = AnchorEntity(plane: .any)
        let dimensions: SIMD3<Float> = [1.2,0.04,0.8]
        let screenMesh = MeshResource.generatePlane(width: dimensions.x, depth: dimensions.z)
        let screenMat = SimpleMaterial(color: .black,roughness: 0.2, isMetallic: false)
        let scrennEntity = ModelEntity(mesh: screenMesh, materials: [screenMat])
        anchor.addChild(scrennEntity)
        arView.scene.addAnchor(anchor)
        arView.tapGesture()
        scrennEntity.generateCollisionShapes(recursive: true)
        arView.installGestures([.translation, .scale], for: scrennEntity)
        return arView
    }
    func updateUIView(_ uiView: ARView, context: Context) {}
}
extension ARView{
    func tapGesture() {
        let tapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(handleTap(recognize:)))
        self.addGestureRecognizer(tapGestureRecognizer)
    }
    @objc func handleTap(recognize:UITapGestureRecognizer){
        let  tapLocation = recognize.location(in: self)
        if let entity  = self.entity(at: tapLocation) as? ModelEntity {
            let videoURL = Bundle.main.url(forResource: "TVShow", withExtension: "mp4")!
            let playerItem = AVPlayerItem(asset: AVAsset(url: videoURL))
            let player = AVPlayer(url: videoURL)
            entity.model?.materials = [VideoMaterial(avPlayer: player)]
            player.replaceCurrentItem(with: playerItem)
            player.play()
        }
    }
}
```
