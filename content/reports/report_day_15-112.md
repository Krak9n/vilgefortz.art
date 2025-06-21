+++
date = '2025-06-21T14:54:46+02:00'
draft = false
title = 'Report Day 15-112'
summary = 'just read me'
+++

20 days have passed. Time flies really rapidly. 
Since the last report i have few things to announce. 

+ I finished third year of my CS learning with pretty high GPA. 
    Perhaps even first in my group. Yeah, that was hard to achieve.
+ The second thing is that i have finally started my way into graphics programming.
    I am currently trying to understand basic OpenGL. But more below

As i adressed it, my journey with OpenGl has begun. 
In first few days i was able to firstly produce a black window and then getting my first **two** triangles.

![triangles](/images/wtf1.png)

Have to admit i am pretty proud of them. 

My current code looks like this: 
```c
#include <glad/glad.h>
#include <GLFW/glfw3.h>

//#include <math.h>
#include <stdio.h>
#include <string.h>
#include "cglm/cglm.h"

#include <ft2build.h>
#include FT_FREETYPE_H  

#define MAX_SIZE 100

struct integers
{
  unsigned int VBO[2], VAO[2];
  unsigned int EBO, shaderProgram, shaderProgramSecondTriangle, Vshader, VshaderSecond, Fshader, FshaderSecond;

} INTs;

struct characters
{
  ivec2 Size;
  ivec2 Bearing;
  unsigned int TextureID;  // ID handle of the glyph texture
  unsigned int Advance;
} cha;

struct values_for_map
{
  int size; // Current number of elements in the map
  char keys[MAX_SIZE][100]; // Array to store the keys
  int values[MAX_SIZE]; 
} f_map;

float first_triangle[] = 
{
  0.0f, 0.5f, 0.0f, 1.0f, 0.0f, 0.0f, // top
  -0.2f, 0.0f, 0.0f, 0.0f, 1.0f, 0.0f, // bottom left
  0.0f, -0.2f, 0.0f, 0.0f, 0.0f, 1.0f, // bottom middle
};

float second_triangle[] = 
{
  0.0f, 0.5f, 0.0f, 1.0f, 0.0f, 0.0f, // top
  0.2f, 0.0f, 0.0f, 0.5f, 0.5f, 0.0f, // bottom right
  0.0f, -0.2f, 0.0f, 0.0f, 0.0f, 1.0f, // bottom middle
};

void init_glfw();
void processInput(GLFWwindow *window);
void framebuffer_size_callback(GLFWwindow* window, int width, int heigth);
void freetype_loader();


int getIndex(char key[]);
void insert(char key[], int value);
int get(char key[]);
void printMap();

static void cursor_position_callback(GLFWwindow* window, double xpos, double ypos);

void shader_get();
void linking_shaders();

const int SCR_HEIGHT = 1920;
const int SCR_WIDTH = 1080;

// will be used for making two colored backround
const int HALF_SCR_HEIGHT = SCR_HEIGHT / 2;
const int HALF_SCR_WIDTH = SCR_WIDTH / 2;

const char *VshaderSource = 
    "#version 440 core\n"
    "layout (location = 0) in vec3 aPos;\n"
    "layout (location = 1) in vec3 aColor;\n"
    "out vec3 ourColor;\n"
    "void main()\n"
    "{\n"
    "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
    "   ourColor = aColor;\n"
    "}\0";

const char *VshaderSecondSource = 
    "#version 440 core\n"
    "layout (location = 0) in vec3 aPos;\n"
    "layout (location = 1) in vec3 aColor1;\n"
    "out vec3 ourColor1;\n"
    "void main()\n"
    "{\n"
    "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
    "   ourColor1 = aColor1;\n"
    "}\0";


const char *FshaderSource = 
    "#version 440 core\n"
    "out vec4 FragColor;\n"
    "in vec3 ourColor;\n"
    "void main()\n"
  "{\n"
    "   FragColor = vec4(ourColor, 1.0f);\n"
    "}\n\0";

const char *FshaderSecondSource = 
    "#version 440 core\n"
    "out vec4 FragColor1;\n"
    "in vec3 ourColor1;\n"
    "void main()\n"
    "{\n"
    "   FragColor1 = vec4(ourColor1, 1.0f);\n"
    "}\n\0";

// the ﬁrst process of the graphics pipeline: the vertex shader. 
int main()
{
  init_glfw();
  // change later
  /*
  ivec2 Size1 = GLM_IVEC2_ONE_INIT; 
  ivec2 Bearing1 = GLM_IVEC2_ONE_INIT;
  
  memcpy(cha.Size, Size1, sizeof(Size1));
  memcpy(cha.Bearing, Bearing1, sizeof(Bearing1));
  */
  // --------------------------
  


  double xpos, ypos;

  GLFWwindow* window = glfwCreateWindow(SCR_HEIGHT, SCR_WIDTH, "welcome to the hell boy", NULL, NULL);
  if(window == NULL)
  {
    printf("failed to create glfw\n");
    glfwTerminate();
    return -1;
  }
  glfwMakeContextCurrent(window);
  glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

  glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_NORMAL);

  // to be notified when cursor moves over the window
  glfwSetCursorPosCallback(window, cursor_position_callback);
  glfwSwapInterval(1);
  glfwSetCursorPos(window, xpos, ypos);
  // mouse cursor is not displayed
  // text isnt on screen. presented in kinsole tho

  if(!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
  {
    printf("failed to initialize glad\n");
    return -1;
  }
  
  // generating vertex and fragment shaders
  shader_get();
  //linking ones before
  linking_shaders();

  glGenVertexArrays(2, INTs.VAO);
  glGenBuffers(2, INTs.VBO);
  
  // first triangle render
  glBindVertexArray(INTs.VAO[0]);
  glBindBuffer(GL_ARRAY_BUFFER, INTs.VBO[0]);
  glBufferData(GL_ARRAY_BUFFER, sizeof(first_triangle), first_triangle, GL_STATIC_DRAW); 
  glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
  glEnableVertexAttribArray(0);
  glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*) (3 * sizeof(float)));
  glEnableVertexAttribArray(1);

  // second triangle
  glBindVertexArray(INTs.VAO[1]);
  glBindBuffer(GL_ARRAY_BUFFER, INTs.VBO[1]);
  glBufferData(GL_ARRAY_BUFFER, sizeof(second_triangle), second_triangle, GL_STATIC_DRAW);
  glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
  glEnableVertexAttribArray(0);
  glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*) (3 * sizeof(float)));
  glEnableVertexAttribArray(1);

  glBindBuffer(GL_ARRAY_BUFFER, 0);
  // glColorPointer(4, GL_FLOAT, 0, colors);

  /* ------------------------------- */
  // getting text
  FT_Library ft;
  if (FT_Init_FreeType(&ft))
  {
    printf("freetype error, could not init FreeType Library\n");
    return -1;
  }

  char font_path[41] = "/usr/share/fonts/TTF/DejaVuSans-Bold.ttf";
  FT_Face face;
  if (FT_New_Face(ft, font_path, 0, &face))
  {
    printf("freetype error, failed to load font\n");  
    return -1;
  } else {

    FT_Set_Pixel_Sizes(face, 0, 48);
    if (FT_Load_Char(face, 'X', FT_LOAD_RENDER))
    {
      printf("ERROR::FREETYTPE: Failed to load Glyph\n");  
      return -1;
    } 
    glPixelStorei(GL_UNPACK_ALIGNMENT, 1); // disable byte-alignment restriction
  
    for (unsigned char c = 0; c < 128; c++)
    {
      // load character glyph 
      if (FT_Load_Char(face, c, FT_LOAD_RENDER))
      {
        printf("ERROR::FREETYTPE: Failed to load Glyph\n");
        continue;
      }
      // generate texture
      unsigned int texture;
      glGenTextures(1, &texture);
      glBindTexture(GL_TEXTURE_2D, texture);
      glTexImage2D(
        GL_TEXTURE_2D,
        0,
        GL_RED,
        face->glyph->bitmap.width,
        face->glyph->bitmap.rows,
        0,
        GL_RED,
        GL_UNSIGNED_BYTE,
        face->glyph->bitmap.buffer
      );
      // set texture options
      glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
      glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
      glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
      glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
      // now store character for later use
      cha = {
        texture, 

        // example of accessing
        ivec2 Size1 = GLM_IVEC2_ONE_INIT(face->glyph->bitmap.width, face->glyph->bitmap.rows); 
        ivec2 Bearing1 = GLM_IVEC2_ONE_INIT(face->glyph->bitmap_left, face->glyph->bitmap_top);
        memcpy(cha.Size, Size1, sizeof(Size1));
        memcpy(cha.Bearing, Bearing1, sizeof(Bearing1));

        face->glyph->advance.x
      };

      //strcpy(val.key, cha);
      // problem here
      //insert(cha);
      memcpy(f_map.values, cha, sizeof(cha));
      //Characters.insert(std::pair<char, Character>(c, character));
    }
  }

  while(!glfwWindowShouldClose(window))
  {
    processInput(window);

    // change in the future to white VBO
    // background of the top part
    glClearColor(0.9f, 0.7f, 0.8f, 0.5f);
    // background of the bottom part
    glClear(GL_COLOR_BUFFER_BIT);

    // left triangle
    glUseProgram(INTs.shaderProgram);
    glBindVertexArray(INTs.VAO[0]);
    glDrawArrays(GL_TRIANGLES, 0, 3);

    // right triangle
    glUseProgram(INTs.shaderProgramSecondTriangle);
    glBindVertexArray(INTs.VAO[1]);
    glDrawArrays(GL_TRIANGLES, 0, 3);
    
    glfwSwapBuffers(window);
    glfwPollEvents();
  }
  
  glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
  
  glfwDestroyWindow(window);
  glfwTerminate();
    
  return 0;
}

void shader_get()
{
  // vertex shader
  INTs.Vshader = glCreateShader(GL_VERTEX_SHADER);
  glShaderSource(INTs.Vshader, 1, &VshaderSource, NULL);
  glCompileShader(INTs.Vshader);
  int success;
  char infoLog[512];
  glGetShaderiv(INTs.Vshader, GL_COMPILE_STATUS, &success);
  // checks for errors while compiling
  if(!success)
  {
    glGetShaderInfoLog(INTs.Vshader, 512, NULL, infoLog);
    printf("error while compiling vertex shader %s\n", infoLog);
  }

  // fragment shader
  INTs.Fshader = glCreateShader(GL_FRAGMENT_SHADER);
  glShaderSource(INTs.Fshader, 1, &FshaderSource, NULL);
  glCompileShader(INTs.Fshader);
 
  glGetShaderiv(INTs.Fshader, GL_COMPILE_STATUS, &success);
  if(!success)
  {
    glGetShaderInfoLog(INTs.Fshader, 512, NULL, infoLog);
    printf("error while compiling fragment shader %s\n", infoLog);
  }
 
  // vertex shader for the second triangle 
  INTs.VshaderSecond = glCreateShader(GL_VERTEX_SHADER);
  glShaderSource(INTs.VshaderSecond, 1, &VshaderSecondSource, NULL);
  glCompileShader(INTs.VshaderSecond);
  
  glGetShaderiv(INTs.VshaderSecond, GL_COMPILE_STATUS, &success);
  // checks for errors while compiling
  if(!success)
  {
    glGetShaderInfoLog(INTs.Vshader, 512, NULL, infoLog);
    printf("error while compiling vertex shader %s\n", infoLog);
  }


  // fragment shader for the second triangle
  INTs.FshaderSecond = glCreateShader(GL_FRAGMENT_SHADER);
  glShaderSource(INTs.FshaderSecond, 1, &FshaderSecondSource, NULL);
  glCompileShader(INTs.FshaderSecond);
  
  glGetShaderiv(INTs.FshaderSecond, GL_COMPILE_STATUS, &success);
  if(!success)
  {
    glGetShaderInfoLog(INTs.FshaderSecond, 512, NULL, infoLog);
    printf("error while compiling fragment shader %s\n", infoLog);
  }
 
}

void linking_shaders()
{
  // for the first triangle
  shader_get();
  int success;
  char infoLog[512];
  // linking shaders 
  INTs.shaderProgram = glCreateProgram();
  glAttachShader(INTs.shaderProgram, INTs.Vshader);
  glAttachShader(INTs.shaderProgram, INTs.Fshader);
  glLinkProgram(INTs.shaderProgram);
  // checks for the errors while linking shaders   
  glGetProgramiv(INTs.shaderProgram, GL_LINK_STATUS, &success);
  if (!success) {
      glGetProgramInfoLog(INTs.shaderProgram, 512, NULL, infoLog);
      printf("error while linking %s\n", infoLog);
  }

  // for the second triangle
  INTs.shaderProgramSecondTriangle = glCreateProgram();
  glAttachShader(INTs.shaderProgramSecondTriangle, INTs.VshaderSecond);
  glAttachShader(INTs.shaderProgramSecondTriangle, INTs.FshaderSecond);
  glLinkProgram(INTs.shaderProgramSecondTriangle);
  // checks for the errors while linking shaders
  glGetProgramiv(INTs.shaderProgramSecondTriangle, GL_LINK_STATUS, &success);
  if (!success) {
      glGetProgramInfoLog(INTs.shaderProgramSecondTriangle, 512, NULL, infoLog);
      printf("error while linking %s\n", infoLog);
  }

  // also could add some checking for errors while compiling but anyway xDDD
  // deleting shaders 'cause those were already linked
  glDeleteShader(INTs.Vshader);
  glDeleteShader(INTs.Fshader);
  glDeleteShader(INTs.VshaderSecond);
  glDeleteShader(INTs.FshaderSecond);
  
}

// return here 
static void cursor_position_callback(GLFWwindow* window, double xpos, double ypos)
{
  glfwGetCursorPos(window, &xpos, &ypos);
  printf("mouse position is,\nx: %lf\ny: %lf\n", xpos, ypos);
}

void processInput(GLFWwindow *window)
{
  if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
  {
    glfwSetWindowShouldClose(window, true);
  }
}

void framebuffer_size_callback(GLFWwindow* window, int width, int heigth)
{
  glViewport(0, 0, width, heigth);
}

void init_glfw()
{
  glfwInit();  

  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 4);
  glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_COMPAT_PROFILE);
}

// Function to get the index of a key in the keys array
int getIndex(char key[])
{
    for (int i = 0; i < f_map.size; i++) {
        if (strcmp(f_map.keys[i], key) == 0) {
            return i;
        }
    }
    return -1; // Key not found
}

// Function to insert a key-value pair into the map
void insert(char key[], int value)
{
    int index = getIndex(key);
    if (index == -1) { // Key not found
        strcpy(f_map.keys[f_map.size], key);
        f_map.values[f_map.size] = value;
        f_map.size++;
    }
    else { // Key found
        f_map.values[index] = value;
    }
}

// Function to get the value of a key in the map
int get(char key[])
{
    int index = getIndex(key);
    if (index == -1) { // Key not found
        return -1;
    }
    else { // Key found
        return f_map.values[index];
    }
}

// Function to print the map
void printMap()
{
    for (int i = 0; i < f_map.size; i++) {
        printf("%s: %d\n", f_map.keys[i], f_map.values[i]);
    }
}

```


I know that's a lot. I'll probably have to divide it into files.
Few things that this awful piece of graphics does:
+ rendering two triangles with a bit different shaders
+ getting mouse position and displaying it into terminal
+ unfinished code here is my tries into displaying text unto screen, however i have problems with translating Cpp code in C.
    learnopengl.com uses cpp as the language for writing, in which there is stuff like f.e. `std::map<char, name> /*name of strcut*/ Characters`, or `glm::ivec2 Size`. 
    Sure, there are libraries like [cglm](https://github.com/recp/cglm) that can help with the last example, but in general i tend to write everything myself.

Thanks for reading this report. 
If you have any suggestions on how to solve my current strugles, please contact me via email -> **olhnts12@proton.me**

![lose](/images/lossing.jpg)
*me after coding for 7 hours straight*
