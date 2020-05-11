---
title: "Animaciones, transiciones y estados visuales"
header:
  image: /assets/posts/es/animaciones-transiciones-y-estados-visuales/header.jpg
tags: [ Español, Visual Studio, UWP, XAML, App, C# ]
categories: [ XAML, UWP ]
lang: es
ref: 1
permalink: /es/animaciones-transiciones-y-estados-visuales/
---

Según avanza la tecnología móvil, vemos como se incrementan las peticiones por parte de los usuarios de aplicaciones cada vez más potentes, con más requisitos, más información y un diseño superior. En muchos casos, se puede observar cómo se valoran más aquellas aplicaciones que sorprenden con un diseño innovador, dinámico e interactivo por encima de aquellas otras que ofrecen más datos o funcionalidad.

En este post vamos a introducir al lector sobre cómo puede, de manera sencilla, ofrecer valor en su aplicación con un diseño dinámico brindado por las animaciones, transiciones y estados visuales.

Pese a que muchos de los controles, páginas y contenedores incluyen de manera determinada animaciones, este post está pensado para aquellos desarrolladores que quieren tener un mayor control de su aplicación y desean generar sus propias animaciones.

El código usado en los ejemplos para mostrar las animaciones disponibles se encuentra disponible en [GitHub](https://github.com/danimart1991/WUAAnimationsIntroduction).

> Nota: Antes de comenzar, cabe destacar que este post está pensado para introducir al desarrollador en animaciones destinadas a Universal Apps (Windows 8.1 y Windows Phone 8.1), pero, por otro lado, muchas de las indicaciones aquí mostradas son válidas para aplicaciones WPF e inclusive Universal Windows Apps (Windows 10).

## Transiciones

Una transición es aquel momento durante la ejecución de la aplicación en el que ocurre un suceso que produce algún cambio. Esto incluye la entrada a una página, añadir o eliminar elementos de una lista, cuando un popup es mostrado, un control cambia de posición…

Muchas de estas animaciones de transición son ofrecidas por una API cuyos nombres contienen “ThemeTransition”, y que están destinados a mostrar animaciones de una manera sencilla ante estos sucesos.

Por ejemplo, pensemos en un botón, al crearlo en XAML, este es mostrado sin más en nuestra aplicación, pero si incluimos dentro de su listado de transiciones “EntranceThemeTransition” con la propiedad “FromHorizontalOffset” a “-200” conseguiremos que el botón se deslice desde la izquierda al cargar la página donde está contenido.

```xml
<Button Content="Transition Button">
    <Button.Transitions>
        <TransitionCollection>
            <EntranceThemeTransition FromHorizontalOffset="-200"/>
        </TransitionCollection>
    </Button.Transitions>
</Button>
```

La mayoría de transiciones contienen propiedades para poder configurar la forma en la que actúan y con ello crear pequeñas diferencias atractivas al usuario final.

También puede darse el caso de querer aplicar transiciones ya no solo a un solo control aislado, sino a contenedores o colecciones de elementos, en estos casos, tenemos la opción de aplicar la transición a cada uno de los elementos, pero con esto conseguiríamos que todos ejecutasen la transición al mismo tiempo, para solventar esto y crear una animación en serie, debe ser el propio contenedor el encargado de recibir la colección de transiciones que queremos aplicar a los objetos que contiene mediante la propiedad *“ItemContainerTransitions”*.

```xml
<ItemsControl>
    <ItemsControl.ItemContainerTransitions>
        <TransitionCollection>
            <EntranceThemeTransition/>
        </TransitionCollection>
    </ItemsControl.ItemContainerTransitions>
    <ItemsControl.ItemsPanel>
        <ItemsPanelTemplate>
            <WrapGrid Height="400"/>
        </ItemsPanelTemplate>
    </ItemsControl.ItemsPanel>
    <ItemsControl.Items>
        <Rectangle Fill="CornflowerBlue" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="Green" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="Red" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="Gray" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="Blue" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="Orange" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="White" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="Violet" Width="50" Height="50" Margin="10"/>
        <Rectangle Fill="Yellow" Width="50" Height="50" Margin="10"/>
    </ItemsControl.Items>
</ItemsControl>
```

Además, podemos incluir transiciones en los hijos de un control que actúe como contenedor, como puede ser un *“Grid”*, mediante la propiedad *“ChildrenTransitions”* para aquellos objetos que pueden contener más de un elemento.

```xml
<Grid>
    <Grid.ChildrenTransitions>
        <TransitionCollection>
            <EntranceThemeTransition/>
        </TransitionCollection>
    </Grid.ChildrenTransitions>
    <!-- Controles hijos -->
</Grid>
```

O incluso en el contenido de un control (y por tanto no actúe sobre el propio control) mediante *“ContentTransitions”* para aquellos objetos que sólo pueden contener un elemento.

```xml
<Button Content="Button">
    <Button.ContentTransitions>
        <TransitionCollection>
            <EntranceThemeTransition FromHorizontalOffset="-200"/>
        </TransitionCollection>
    </Button.ContentTransitions>
</Button>
```

La lista completa de transiciones disponibles y su uso es la siguiente:

| API | Descripción |
|-----|-------------|
| AddDeleteThemeTransition | Ofrece el comportamiento de transición animada cuando los controles agregan o eliminan objetos secundarios o contenido. El control suele ser un contenedor de elementos. |
| ContentThemeTransition | Proporciona el comportamiento de transición animada correspondiente a cuando cambia el contenido de un control. Puedes aplicar esta animación junto con AddDeleteThemeTransition. |
| EdgeUIThemeTransition | Proporciona un comportamiento de transición animada para una interfaz de usuario de borde pequeña. Suele usarse para mostrar elementos que no se acerquen al centro de la pantalla y quieran ser mostrados desde el borde, como una barra de error o la AppBar. |
| EntranceThemeTransition | Ofrece el comportamiento de transición animada cuando los controles aparecen por primera vez.Nota: Si la página queda en cache, la siguiente navegación realizada a ella no ejecuta este tipo de animaciones. |
| PaneThemeTransition | Proporciona un comportamiento de transición animada para una interfaz de usuario de borde de panel (grande). Suele usarse para mostrar elementos que se deslizan una distancia significativa en la pantalla, como un panel de tareas o un teclado en pantalla. |
| PopupThemeTransition | Proporciona el comportamiento de transición animada que se aplica a los componentes de controles emergentes (por ejemplo, un elemento que muestra un mensaje por pantalla) cuando aparecen. |
| ReorderThemeTransition | Ofrece el comportamiento de transición animada cuando los elementos de los controles de vista de lista cambian de orden. Esto suele ocurrir como resultado de una operación de arrastrar y soltar. Los temas y controles diferentes pueden tener características distintas en las animaciones. |
| RepositionThemeTransition | Ofrece el comportamiento de transición animada cuando los controles cambian de posición. |

A continuación, [un vídeo](https://www.youtube.com/watch?v=XBXOUcj4dEQ) muestra las distintas transiciones existentes, así como el código utilizado. Como hemos visto, estas transiciones pueden ser usadas a distintos niveles dentro de un mismo control.

{% include video id="XBXOUcj4dEQ" provider="youtube" %}

## Guiones gráficos

Cuando se desea tener todavía más control de las animaciones, y queremos ser nosotros mismos como desarrolladores los encargados de su ejecución, los guiones gráficos (Storyboards) y sus “ThemeAnimations” son una buena elección a considerar.

Al contrario que las transiciones, los guiones gráficos (Storyboards) son los encargados de introducir los “ThemeAnimations” con el fin de llevar a cabo las animaciones. Por tanto, el desencadenador integrado, que estaba presente en las transiciones, deja paso a un control más personalizado.

Estos “ThemeAnimations” contenidos en “Storyboards”, pueden actuar sobre distintas propiedades y eventos de los controles, pero no pueden generarse animaciones nuevas más allá de su uso en el ambiente elegido por el desarrollador. Es decir, las animaciones están predeterminadas por el conjunto de API que contiene el nombre “ThemeAnimation”, pero estas pueden ser mostradas cuando el desarrollador requiera mediante el uso de “Storyboards”.

```xml
<Grid>
    <Grid.Resources>
        <Storyboard x:Name="MyStoryboard">
            <FadeOutThemeAnimation TargetName="MyButton" />
        </Storyboard>
    </Grid.Resources>
    <Button Click="MyButton_Click" Content="MyButton" x:Name="MyButton"/>
</Grid>
```

```csharp
private void MyButton_Click(object sender, RoutedEventArgs e)
{
    MyStoryboard.Begin();
}
```

En este ejemplo podemos ver un botón que al ser presionado desaparece. La mayoría de “ThemeAnimations” incluyen propiedades para controlar la duración de la animación, o si queremos que empiece en un tiempo predeterminado. Desde aquí recomendamos echar un vistazo a todas ellas.

Lista completa de “ThemeAnimations” con su descripción:

| API | Descripción |
|-----|-------------|
| DragItemThemeAnimation | Representa la animación preconfigurada que se aplica a los elementos que se arrastran. |
| DragOverThemeAnimation | Representa la animación preconfigurada que se aplica a los elementos que se encuentran debajo de los que se arrastran. |
| DropTargetItemThemeAnimation | La animación preconfigurada que se aplica a elementos potenciales de destino para soltar. |
| FadeInThemeAnimation | La animación de opacidad preconfigurada que se aplica a los controles cuando aparecen por primera vez. |
| FadeOutThemeAnimation | La animación de opacidad preconfigurada que se aplica a los controles cuando se quitan de la interfaz de usuario o se ocultan. |
| PointerDownThemeAnimation | La animación preconfigurada de acciones de usuario que pulsan o hacen clic en un elemento. |
| PointerUpThemeAnimation | La animación preconfigurada para la acción de usuario que se ejecuta después de que el usuario pulsa un elemento y se libera la acción. |
| PopInThemeAnimation | La animación preconfigurada que se aplica a componentes emergentes de los controles según aparecen. Esta animación combina opacidad y traducción. |
| PopOutThemeAnimation | La animación preconfigurada que se aplica a componentes emergentes de los controles según se cierran o se quitan. Esta animación combina opacidad y traducción. |
| RepositionThemeAnimation | La animación preconfigurada de un objeto según cambia de posición. |
| SplitCloseThemeAnimation | La animación preconfigurada que oculta una UI de destino que usa una animación de división. |
| SplitOpenThemeAnimation | La animación preconfigurada que revela una UI de destino que usa una animación de división. |
| SwipeBackThemeAnimation | La animación preconfigurada que se aplica a los controles cuando un elemento vuelve a su lugar en el diseño tras una interacción de deslizar rápidamente. |
| SwipeHintThemeAnimation | La animación preconfigurada que indica que ahora es posible un gesto de deslizar rápidamente. |

A continuación, [un vídeo](https://www.youtube.com/watch?v=mnRo8y2g4Io) muestra las distintas transiciones existentes.

{% include video id="mnRo8y2g4Io" provider="youtube" %}

Por otro lado, aunque puede usarse el método descrito en el ejemplo anterior para realizar la ejecución del “Storyboard”, mediante el uso de “Begin()”, no es el uso habitual. Estos suelen estar contenidos dentro de “VisualStates” que indiquen al control el momento en el que deben ser ejecutados o parados.

## Estados Visuales

A grandes rasgos, un estado visual es una técnica usada cuando queremos realizar un cambio en el control que modificará la interfaz en XAML. Gracias a los estados visuales podemos por ejemplo hacer que un botón tenga el estado normal, presionado, deshabilitado, y que el usuario final sea capaz de identificar estos estados, y al desarrollador no le sea difícil implementarlos.

Los “VisualStates” suelen crearse dentro de grupos (“VisualStateGroup”) para que así unos se anulen a otros y solo esté ejecutándose un estado visual al mismo tiempo.

Dado que el post es una introducción a las animaciones, no vamos a hacer hincapié en los “VisualStates”, y simplemente remitimos a que muchos controles ya incluyen estados visuales en sus plantillas que pueden ser modificados para incluir animaciones o “ThemeTransitions” nuevos dentro de sus “Storyboards”. Sin embargo, el nombre de estos estados visuales no puede ser modificado si se desea el mismo comportamiento.

```xml
<Style x:Key="BackButtonStyle" TargetType="Button">
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="Button">
                <Grid x:Name="RootGrid">
                ...
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal"/>
                        ...
                        <VisualState x:Name="Disabled">
                            <Storyboard>
                                <ObjectAnimationUsingKeyFrames
                                    Storyboard.TargetName="RootGrid"
                                    Storyboard.TargetProperty="Visibility">
                                    <DiscreteObjectKeyFrame Value="Collapsed" KeyTime="0"/>
                                </ObjectAnimationUsingKeyFrames>
                            </Storyboard>
                        </VisualState>
                    </VisualStateGroup>
                    ...
                </VisualStateManager.VisualStateGroups>
                </Grid>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

Así mismo, el usuario puede crear nuevos estados visuales y ejecutarlos por código usando

```csharp
VisualStateManager.GoToState(Control control, string nombreEstadoVisual, bool usarTransiciones);
```

## Conclusión

Con estas últimas líneas de código, damos por cerrada la introducción a las animaciones para aplicaciones Windows. A partir de ahora tus aplicaciones serán más dinámicas, vivas y animadas.
