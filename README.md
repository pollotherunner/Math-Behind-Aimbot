ola, nesse paper irei mostrar a matematica por tras de um recurso muito utilizado por players de todo o mundo para trapacear em jogos online, o aimbot!

ao contrario do que todos pensam,  a matematica aprendida na escola sera sim algo util, se voce quiser fazer um cheat, porque para todo resto é inutil, mas vamos la

**1 - Magnitude (distancia pros noobs)**

a magnitude/distancia é o comprimento do vetor, resumindo: distancia entre vc e a entidade.
calculamos da seguinte forma: 

```CPP
double magnitude = sqrt(pow(entities_xyz.x - localplayer_xyz.x,2) + pow(entities_xyz.y - localplayer_xyz.y,2) + pow(entities_xyz.z - localplayer_xyz.z, 2));
```

- ``sqrt``: função da biblioteca cmath.h para calcular raiz quadrada
- ``pow``: função da biblioteca cmath.h para calcular potenciação, sendo o segundo argumento o numero elevado ao primeiro argumento
  
**2 - Distancia entre sua head e a head da entidade (Vector3)**

voce tambem prescisara calcular a distancia entre a sua cabeça e a cabeça do inimigo, o resultado devera sair em Vector3

na biblioteca de geometria que eu uso existe operadores para todas as operaçoes basicas utilizando vetores, entao nao vai ser um problema pra mim, nem pra voce ja que vou disponibilizar a biblioteca (by fael):

```cpp
class Vector3D final
{
public:

    float x, y, z;

    Vector3D(const float x, const float y, const float z) : x(x), y(y), z(z) {}
    Vector3D operator + (const Vector3D& rhs) const { return Vector3D(x + rhs.x, y + rhs.y, z + rhs.z); }
    Vector3D operator - (const Vector3D& rhs) const { return Vector3D(x - rhs.x, y - rhs.y, z - rhs.z); }
    Vector3D operator * (const float& rhs) const { return Vector3D(x * rhs, y * rhs, z * rhs); }
    Vector3D operator / (const float& rhs) const { return Vector3D(x / rhs, y / rhs, z / rhs); }
    bool operator == (const Vector3D& rhs) const { return x == rhs.x && y == rhs.y && z == rhs.z; }
    Vector3D& operator += (const Vector3D& rhs) { return *this = *this + rhs; }
    Vector3D& operator -= (const Vector3D& rhs) { return *this = *this - rhs; }
    Vector3D& operator *= (const float& rhs) { return *this = *this * rhs; }
    Vector3D& operator /= (const float& rhs) { return *this = *this / rhs; }
    float Length() const { return sqrt(x * x + y * y + z * z); }
    Vector3D Normalize() const { return *this * (1 / Length()); }
    Vector3D Multiple(const Vector3D caca) const { return Vector3D(x * caca.x, y * caca.y, z * caca.z); }

    Vector3D() : x(0.0f), y(0.0f), z(0.0f) {}
};
```


agora é so subtrair:

```cpp
Vector3D headPosDist = localplayer_headpos - entity_head_pos;
```

voce prescisa ter em mente que quando voce declara um endereço com uma offset usando Vector3, o computador vai entender que voce vai querer pegar o valor de onde vc indicou e tambem +2 posiçoes a frente, entao se eu faço isso:

```cpp
memory::Read<Vector3D>(entity_address + CPlayer::Head)
```

estou querendo dizer que quero o **CPlayer::Head** + 2 posiçoes na memoria, neste caso se

```
CPlayer::Head é igual a  0x04
o valor declarado em Vector3 sera:
x = 0x4, y = 0x8, z = 0xC*`
```

**3 - Trigonometria**

pronto, agora ja sabemos onde devemos mirar, so falta calcular o angulo para o yaw e para o pitch (x,y) respectivamente

para calcularmos o angulo prescisaremos utilizar a função `-atan2` , o "-" é porque queremos calcular o oposto

yaw:

```cpp
float yaw = -atan2(headPosDist.x , headPosDist.y);
```

pitch:

```cpp
float pitch = -atan2(headPosDist.z, headPosDist.CalcHipotenusa());
```

função `CalcHipotenusa()`:

```cpp
float CalcHipotenusa()
{
    float squaresum = x * x + y * y + z * z;
    return sqrt(squaresum);
}
```

a função `CalcHipotenusa()` é literalmente oque nos fizemos para calcular magnitude porem sem a subtração

**4 - Convertendo radianos em graus**

a função `-atan2` vai nos dar o resultado em radianos, portanto iremos converter-lo em graus, é bem simples fazer isso, apenas multiplicar o valor por `180/PI` 

Exemplos:

```cpp
float yaw = -atan2(headPosDist.x , headPosDist.y);
yaw *= 180 / PI;
float pitch = -atan2(headPosDist.z, headPosDist.CalcHipotenusa());
pitch *= 180 / PI;
```

agora é só escrever isso esse valor na memoria, e pronto temos um aimbot, to com preguiça de explicar smooth ou fov mas vc pesquise por si proprio, a base eu ja te dei.

**Fontes:**

https://www.unknowncheats.me/forum/counterstrike-global-offensive/137492-math-behind-hack-1-coding-better-aimbot-stop-using-calcangle.html

https://www.youtube.com/@cazz

https://www.youtube.com/@GuidedHacking


**Menção Honrrosa:**

cv4ck
friaca
fael
spook


feito com amor por pollotherunner on discord :)
