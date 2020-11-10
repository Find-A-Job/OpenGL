# OpenGL

------
[参考资料](https://learnopengl-cn.readthedocs.io/zh/latest/01%20Getting%20started/06%20Textures/#_1)
------
### 着色器
编写着色器源码，编译着色器，创建空的着色器对象，与刚编译好的着色器进行绑定<br>
可以创建多个着色器对象，在绘制前调用gl.useprogram(prog)即可启用
```
func getFShader2() string {
	fs := `
	   #version 410
	   out vec4 frag_colour;
	   void main() {
	       frag_colour = vec4(0.5, 1, 1, 1);
	   }
	` + "\x00"
	return fs
}
func compileShader(source string, shaderType uint32) (uint32, error) {
	shader := gl.CreateShader(shaderType)

	csources, free := gl.Strs(source)
	gl.ShaderSource(shader, 1, csources, nil)
	free()
	gl.CompileShader(shader)

	var status int32
	gl.GetShaderiv(shader, gl.COMPILE_STATUS, &status)
	if status == gl.FALSE {
		var logLength int32
		gl.GetShaderiv(shader, gl.INFO_LOG_LENGTH, &logLength)

		log := strings.Repeat("\x00", int(logLength+1))
		gl.GetShaderInfoLog(shader, logLength, nil, gl.Str(log))

		return 0, fmt.Errorf("failed to compile %v: %v", source, log)
	}

	return shader, nil
}
...
	vs := getVShader()
	fs := getFShader()
	fs2 := getFShader2()
	if err := gl.Init(); err != nil {
		panic(err)
	}
	vertexShader, err := compileShader(vs, gl.VERTEX_SHADER)
	if err != nil {
		panic(err)
	}
	fragmentShader, err := compileShader(fs, gl.FRAGMENT_SHADER)
	if err != nil {
		panic(err)
	}

	prog := gl.CreateProgram()
	gl.AttachShader(prog, vertexShader)
	gl.AttachShader(prog, fragmentShader)
	gl.LinkProgram(prog)

	prog2 := gl.CreateProgram()
	gl.AttachShader(prog2, vertexShader)
	gl.AttachShader(prog2, fragmentShader2)
	gl.LinkProgram(prog2)
	
...
		gl.UseProgram(prog2)
		gl.BindVertexArray(VAO2)
		gl.DrawElements(gl.TRIANGLES, 6, gl.UNSIGNED_INT, nil)
		gl.BindVertexArray(0)
		
...
```

### 纹理环绕
纹理坐标的范围通常是从(0, 0)到(1, 1)<br>
可修改这个值达到放大缩小的效果<br>
(0.45 - 0.55)局部放大<br>
(-1.0 - 2.0)缩小9倍<br>
(0.0 - 3.0)同上<br>

```
        // 3 * 3 图片九宫格居中
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
