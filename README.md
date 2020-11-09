# OpenGL

------
[参考资料](https://learnopengl-cn.readthedocs.io/zh/latest/01%20Getting%20started/06%20Textures/#_1)
------
### 纹理环绕
3 * 3 图片九宫格居中
```
	verti = []float32{
		// Positions   // Texture Coords
		0.5, 0.5, 0.0, -1.0, 2.0, 
		0.5, -0.5, 0.0, 2.0, 2.0, 
		-0.5, -0.5, 0.0, 2.0, -1.0, 
		-0.5, 0.5, 0.0, -1.0, -1.0, 
	}

	gl.TexParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_BORDER)
	gl.TexParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_BORDER)
```
