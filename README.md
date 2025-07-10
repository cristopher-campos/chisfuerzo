<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chisfuerzo: Aprende y Crece</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .list-disc li::marker {
            color: #2563eb;
        }
        .list-circle li::marker {
            color: #1d4ed8;
        }
        .theory-content p {
            line-height: 1.6;
        }
        .theory-content em {
            font-style: italic;
        }
        .theory-content strong {
            font-weight: 600;
        }
        /* Custom styles for gradients and shadows */
        .gradient-text {
            background-image: linear-gradient(to right, var(--tw-gradient-stops));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            color: transparent;
        }
        .shadow-text-light {
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
        }
        .shadow-text-dark {
            text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
        }
        .card-icon {
            font-size: 4rem; /* Larger icons */
            margin-bottom: 1.25rem;
            transition: transform 0.3s ease-in-out;
        }
        .card-div:hover .card-icon {
            transform: scale(1.1);
        }
        .feedback-animation {
            animation: fadeIn 0.5s ease-out forwards;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="min-h-screen bg-gradient-to-br from-sky-50 to-blue-100 flex flex-col items-center justify-center p-6">

    <div id="app" class="w-full max-w-7xl mx-auto py-10">
    </div>

    <footer class="mt-16 text-center text-gray-600 text-sm">
        <p>App ID: <span id="app-id-display">Cargando...</span></p>
        <p>User ID: <span id="user-id-display">Cargando...</span></p>
        <p>&copy; 2025 Chisfuerzo. Todos los derechos reservados.</p>
    </footer>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        let selectedCourse = null;
        let selectedLevel = 'basico';
        let db = null;
        let auth = null;
        let currentUserId = 'Cargando...';
        let isAuthReady = false;
        let userAnswers = {};
        let score = 0;

        document.getElementById('app-id-display').textContent = appId;

        const initFirebase = async () => {
            if (Object.keys(firebaseConfig).length > 0) {
                try {
                    const app = initializeApp(firebaseConfig);
                    db = getFirestore(app);
                    auth = getAuth(app);

                    if (initialAuthToken) {
                        await signInWithCustomToken(auth, initialAuthToken);
                    } else {
                        await signInAnonymously(auth);
                    }

                    onAuthStateChanged(auth, (user) => {
                        if (user) {
                            currentUserId = user.uid;
                        } else {
                            currentUserId = 'No autenticado';
                        }
                        document.getElementById('user-id-display').textContent = currentUserId;
                        isAuthReady = true;
                    });
                } catch (error) {
                    console.error("Error initializing Firebase:", error);
                    currentUserId = 'Error de Firebase';
                    document.getElementById('user-id-display').textContent = currentUserId;
                    isAuthReady = true;
                }
            } else {
                console.warn("Firebase config not available. Running without Firebase features.");
                currentUserId = 'Sin Firebase';
                document.getElementById('user-id-display').textContent = currentUserId;
                isAuthReady = true;
            }
        };

        const courses = [
            { id: 'matematicas', name: 'Matemáticas', description: 'Explora el mundo de los números y la lógica. 🔢' },
            { id: 'comunicacion', name: 'Comunicación', description: 'Mejora tus habilidades de expresión oral y escrita. 🗣️✍️' },
            { id: 'ingles', name: 'Inglés', description: 'Aprende un nuevo idioma y abre nuevas oportunidades. 🇬🇧🇺🇸' },
            { id: 'minijuegos', name: 'Minijuegos', description: '¡Diviértete con nuestros minijuegos aquí! 🎮', isExternal: true, url: 'https://cristopher-campos.github.io/chisfuerzo-minijuegos/' },
        ];

        const courseContent = {
            matematicas: {
                basico: {
                    theory: `
                        <p class="mb-6">En el nivel Básico de Matemáticas ➕, te familiarizarás con los pilares fundamentales: los números, las operaciones básicas y las formas geométricas elementales. Es el cimiento sobre el cual se construye todo el conocimiento matemático posterior.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Aritmética 🔢</h4>
                            <p class="mb-2">La aritmética se centra en las operaciones con números.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Números Naturales y Enteros:</strong> Números para contar ($\mathbb{N}$) y enteros ($\mathbb{Z}$) que incluyen negativos y cero.</li>
                            <li><strong>Operaciones Básicas:</strong> Suma, resta, multiplicación y división. Conceptos de conmutatividad, asociatividad y la importancia de la división por cero.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Álgebra y Geometría Preliminares 📐</h4>
                            <p class="mb-2">Introducción a ideas que se desarrollarán en niveles posteriores.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Fracciones y Decimales:</strong> Representan partes de un todo y números no enteros. Conversiones básicas.</li>
                            <li><strong>Figuras Planas Básicas:</strong> Reconocimiento de cuadrados, círculos, triángulos y rectángulos.</li>
                            <li><strong>Perímetro y Área:</strong> Distancia alrededor de una figura y espacio que ocupa. Cálculos simples.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Este nivel te dará una base sólida para avanzar en tu viaje matemático. ¡La práctica constante es clave! 💪</p>
                    `,
                    questions: [
                        { id: 'm_b_q1', text: 'Calcula: $15 + 23 - 8$.', type: 'number', answer: 30 },
                        { id: 'm_b_q2', text: 'Si tienes 20 caramelos y los divides entre 4 amigos, ¿cuántos caramelos recibe cada uno?', type: 'number', answer: 5 },
                        { id: 'm_b_q3', text: 'Convierte la fracción $1/2$ a decimal.', type: 'number', answer: 0.5 },
                        { id: 'm_b_q4', text: 'Calcula el perímetro de un rectángulo con lados de 5 cm y 3 cm.', type: 'number', answer: 16 },
                        { id: 'm_b_q5', text: 'Si un cuadrado tiene un lado de 6 cm, ¿cuál es su área en cm$^2$?', type: 'number', answer: 36 },
                        { id: 'm_b_q6', text: '¿Cuál es el resultado de $10 \times 7 + 5$?', type: 'number', answer: 75 },
                        { id: 'm_b_q7', text: 'Si un pastel se divide en 4 partes iguales y te comes 1, ¿qué fracción del pastel te comiste?', type: 'text', answer: '1/4' },
                        { id: 'm_b_q8', text: '¿Cuál es el número entero que representa una deuda de 15 dólares?', type: 'number', answer: -15 },
                        { id: 'm_b_q9', text: 'Si un triángulo tiene lados de 3 cm, 4 cm y 5 cm, ¿cuál es su perímetro?', type: 'number', answer: 12 },
                        { id: 'm_b_q10', text: 'Convierte $0.75$ a fracción.', type: 'text', answer: '3/4' },
                        { id: 'm_b_q11', text: '¿Cuál es el resultado de $25 \div 5 - 3$?', type: 'number', answer: 2 },
                        { id: 'm_b_q12', text: 'Nombra una figura geométrica con 4 lados iguales y 4 ángulos rectos.', type: 'text', answer: 'Cuadrado' },
                        { id: 'm_b_q13', text: 'Si un libro cuesta $8 y una pluma $2, ¿cuánto cuestan 3 libros y 2 plumas?', type: 'number', answer: 28 },
                        { id: 'm_b_q14', text: '¿Cuál es el número natural más pequeño?', type: 'number', answer: 1 },
                        { id: 'm_b_q15', text: 'Calcula el área de un triángulo con base de 10 cm y altura de 4 cm.', type: 'number', answer: 20 },
                        { id: 'm_b_q16', text: 'Si la temperatura es de $5^\circ C$ y baja $7^\circ C$, ¿cuál es la nueva temperatura?', type: 'number', answer: -2 },
                        { id: 'm_b_q17', text: '¿Cuántos minutos hay en 2 horas?', type: 'number', answer: 120 },
                        { id: 'm_b_q18', text: 'Simplifica la fracción $4/8$.', type: 'text', answer: '1/2' },
                        { id: 'm_b_q19', text: 'Si un círculo tiene un radio de 7 cm, ¿cuál es su diámetro?', type: 'number', answer: 14 },
                        { id: 'm_b_q20', text: '¿Cuál es el resultado de $12 \times (5 - 2)$?', type: 'number', answer: 36 },
                    ]
                },
                intermedio: {
                    theory: `
                        <p class="mb-6">En el nivel Intermedio de Matemáticas 📈, profundizaremos en el álgebra, la geometría analítica y trigonometría, y operaciones avanzadas con números.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Álgebra 📊</h4>
                            <p class="mb-2">Uso de símbolos para representar cantidades desconocidas.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Ecuaciones Lineales:</strong> Resolver ecuaciones con una incógnita.</li>
                            <li><strong>Sistemas de Ecuaciones:</strong> Resolver conjuntos de ecuaciones con múltiples incógnitas (sustitución, eliminación).</li>
                            <li><strong>Introducción a Funciones:</strong> Comprender qué es una función y sus representaciones.</li>
                            <li><strong>Polinomios:</strong> Sumar, restar y multiplicar expresiones algebraicas.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Geometría Analítica y Trigonometría 📏</h4>
                            <p class="mb-2">Combinar geometría con álgebra en un plano de coordenadas.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Plano Cartesiano:</strong> Ubicar puntos y graficar figuras con coordenadas $(x, y)$.</li>
                            <li><strong>Distancia y Pendiente:</strong> Calcular distancia entre puntos y la inclinación de una línea.</li>
                            <li><strong>Introducción a la Trigonometría:</strong> Relaciones entre ángulos y lados de triángulos (seno, coseno, tangente).</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Potencias, Raíces y Logaritmos ⚡</h4>
                            <p class="mb-2">Operaciones avanzadas con números y sus inversas.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Propiedades de Potencias:</strong> Reglas para operar con exponentes.</li>
                            <li><strong>Raíces Cuadradas y Cúbicas:</strong> Operaciones inversas de las potencias.</li>
                            <li><strong>Concepto de Logaritmos:</strong> Entender qué son y sus propiedades básicas.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Este nivel te preparará para desafíos matemáticos más avanzados. ¡La práctica constante es tu mejor aliada! 🚀</p>
                    `,
                    questions: [
                        { id: 'm_i_q1', text: 'Resuelve la ecuación: $5x - 12 = 3x + 4$.', type: 'number', answer: 8 },
                        { id: 'm_i_q2', text: 'Si $f(x) = x^2 + 3$, ¿cuánto es $f(2)$?', type: 'number', answer: 7 },
                        { id: 'm_i_q3', text: 'Calcula el valor de $\cos(60^\circ)$.', type: 'number', answer: 0.5 },
                        { id: 'm_i_q4', text: 'Simplifica la expresión: $(x^4 \cdot x^2)^3$.', type: 'text', answer: 'x^18' },
                        { id: 'm_i_q5', text: 'Resuelve para $x$: $\log_3(x) = 2$.', type: 'number', answer: 9 },
                        { id: 'm_i_q6', text: 'Encuentra el valor de $y$ en el sistema: $x+y=7$, $x-y=3$.', type: 'number', answer: 2 },
                        { id: 'm_i_q7', text: '¿Cuál es la pendiente de la línea que pasa por los puntos $(1,1)$ y $(3,5)$?', type: 'number', answer: 2 },
                        { id: 'm_i_q8', text: 'Calcula $\sqrt{144}$.', type: 'number', answer: 12 },
                        { id: 'm_i_q9', text: 'Expresa $10^4$ como un número.', type: 'number', answer: 10000 },
                        { id: 'm_i_q10', text: 'Si $g(x) = 3x - 5$, ¿cuánto es $g(4)$?', type: 'number', answer: 7 },
                        { id: 'm_i_q11', text: 'Resuelve la ecuación: $2(x+3) = 10$.', type: 'number', answer: 2 },
                        { id: 'm_i_q12', text: '¿Cuál es la distancia entre los puntos $(0,0)$ y $(5,12)$?', type: 'number', answer: 13 },
                        { id: 'm_i_q13', text: 'Calcula $\tan(45^\circ)$.', type: 'number', answer: 1 },
                        { id: 'm_i_q14', text: 'Simplifica $ (a^5 / a^2) $.', type: 'text', answer: 'a^3' },
                        { id: 'm_i_q15', text: 'Resuelve para $x$: $\log_5(x) = 1$.', type: 'number', answer: 5 },
                        { id: 'm_i_q16', text: 'Suma los polinomios: $(2x^2 + 3x) + (x^2 - x)$.', type: 'text', answer: '3x^2 + 2x' },
                        { id: 'm_i_q17', text: 'Si un ángulo de un triángulo rectángulo es $30^\circ$, ¿cuál es el otro ángulo agudo?', type: 'number', answer: 60 },
                        { id: 'm_i_q18', text: 'Calcula $\sqrt[3]{27}$.', type: 'number', answer: 3 },
                        { id: 'm_i_q19', text: 'Expresa $0.001$ como una potencia de 10.', type: 'text', answer: '10^-3' },
                        { id: 'm_i_q20', text: 'Multiplica los polinomios: $(x+2)(x-3)$.', type: 'text', answer: 'x^2 - x - 6' },
                    ]
                },
                avanzado: {
                    theory: `
                        <p class="mb-6">En el nivel Avanzado de Matemáticas 🧠, te sumergirás en el cálculo, el álgebra lineal y las ecuaciones diferenciales, bases de la ciencia y la ingeniería moderna.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Cálculo Diferencial 🚀</h4>
                            <p class="mb-2">Estudio de cómo cambian las funciones y la tasa de cambio instantánea.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Límites:</strong> Comportamiento de una función al acercarse a un punto.</li>
                            <li><strong>Derivadas:</strong> Tasa de cambio instantánea de una función.</li>
                            <li><strong>Reglas de Derivación y Aplicaciones:</strong> Técnicas para derivar funciones complejas y su uso en optimización.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Cálculo Integral 📊</h4>
                            <p class="mb-2">Estudio de la acumulación de cantidades y el área bajo las curvas.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Integrales Indefinidas (Antiderivadas):</strong> Encontrar la función original a partir de su derivada.</li>
                            <li><strong>Integrales Definidas y Aplicaciones:</strong> Calcular el área exacta bajo una curva y sus usos.</li>
                            <li><strong>Teorema Fundamental del Cálculo y Técnicas:</strong> Conexión entre diferenciación e integración y métodos avanzados.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Ecuaciones Diferenciales y Álgebra Lineal 🧩</h4>
                            <p class="mb-2">Campos especializados para modelar sistemas complejos.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Ecuaciones Diferenciales:</strong> Ecuaciones que involucran funciones y sus derivadas para modelar cambios.</li>
                            <li><strong>Álgebra Lineal:</strong> Estudio de vectores, matrices y transformaciones lineales.</li>
                            <li><strong>Series y Sucesiones:</strong> Comportamiento de secuencias de números y sumas infinitas.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Este nivel te equipará con herramientas matemáticas poderosas para la investigación y la innovación. ¡El conocimiento es ilimitado!💡</p>
                    `,
                    questions: [
                        { id: 'm_a_q1', text: 'Calcula la derivada de $f(x) = 3x^2 - 5x + 7$.', type: 'text', answer: '6x - 5' },
                        { id: 'm_a_q2', text: 'Evalúa la integral $\int (4x^3) dx$.', type: 'text', answer: 'x^4 + C' },
                        { id: 'm_a_q3', text: '¿Qué tipo de ecuación es $y\'\' + y = 0$?', type: 'text', answer: 'Ecuación diferencial ordinaria' },
                        { id: 'm_a_q4', text: 'Si $A = \\begin{pmatrix} 1 & 0 \\\\ 0 & 1 \\end{pmatrix}$, ¿cuál es la matriz identidad?', type: 'text', answer: 'Sí' },
                        { id: 'm_a_q5', text: '¿Qué es una serie de Taylor?', type: 'text', answer: 'Una representación de una función como una suma infinita de términos calculados a partir de los valores de las derivadas de la función en un solo punto.' },
                        { id: 'm_a_q6', text: 'Calcula $\\lim_{x \\to 0} \\frac{\\sin(x)}{x}$.', type: 'number', answer: 1 },
                        { id: 'm_a_q7', text: 'Si $f(x) = e^{2x}$, ¿cuál es $f\'(x)$?', type: 'text', answer: '2e^2x' },
                        { id: 'm_a_q8', text: 'Evalúa $\\int_{0}^{1} x^2 dx$.', type: 'number', answer: 0.3333 }, // 1/3
                        { id: 'm_a_q9', text: '¿Qué es un vector en álgebra lineal?', type: 'text', answer: 'Un objeto matemático que tiene magnitud y dirección.' },
                        { id: 'm_a_q10', text: 'Resuelve la ecuación diferencial $y\' = 5y$.', type: 'text', answer: 'y = Ce^(5x)' },
                        { id: 'm_a_q11', text: 'Encuentra los puntos críticos de $f(x) = x^3 - 6x^2 + 9x$.', type: 'text', answer: 'x=1, x=3' },
                        { id: 'm_a_q12', text: 'Calcula el producto cruz de los vectores $\\vec{A} = (1,0,0)$ y $\\vec{B} = (0,1,0)$.', type: 'text', answer: '(0,0,1)' },
                        { id: 'm_a_q13', text: '¿Cuál es el teorema fundamental del cálculo?', type: 'text', answer: 'Relaciona la diferenciación y la integración, mostrando que son operaciones inversas.' },
                        { id: 'm_a_q14', text: 'Si una función es continua en un intervalo cerrado $[a,b]$ y diferenciable en $(a,b)$, ¿qué teorema se aplica para garantizar la existencia de un punto $c$ donde la derivada es igual a la pendiente de la secante?', type: 'text', answer: 'Teorema del Valor Medio' },
                        { id: 'm_a_q15', text: 'Evalúa $\\int \\frac{1}{x} dx$.', type: 'text', answer: '\\ln|x| + C' },
                        { id: 'm_a_q16', text: '¿Qué es una transformación lineal en álgebra lineal?', type: 'text', answer: 'Una función entre dos espacios vectoriales que preserva las operaciones de suma de vectores y multiplicación por escalar.' },
                        { id: 'm_a_q17', text: 'Resuelve la ecuación diferencial $y\'\' - 4y = 0$.', type: 'text', answer: 'y = C1e^(2x) + C2e^(-2x)' },
                        { id: 'm_a_q18', text: 'Calcula la derivada de $f(x) = \\ln(x)$.', type: 'text', answer: '1/x' },
                        { id: 'm_a_q19', text: '¿Qué es una serie de Fourier?', type: 'text', answer: 'Una forma de representar funciones periódicas como una suma de senos y cosenos.' },
                        { id: 'm_a_q20', text: 'Encuentra el valor de $\\int_{0}^{\\pi} \\sin(x) dx$.', type: 'number', answer: 2 },
                    ]
                },
            },
            comunicacion: {
                basico: {
                    theory: `
                        <p class="mb-6">En el nivel Básico de Comunicación 🗣️✍️, sentarás las bases para una interacción efectiva, aprendiendo sobre los componentes esenciales del proceso comunicativo y los modos principales de expresión.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Fundamentos de la Comunicación 🤔</h4>
                            <p class="mb-2">La comunicación es el proceso fundamental de intercambiar información, ideas, sentimientos y significados.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Importancia:</strong> Permite coordinar acciones, construir relaciones, expresar emociones, etc.</li>
                            <li><strong>Elementos Clave:</strong> Emisor, Mensaje, Canal, Receptor, Código, Contexto, Retroalimentación (Feedback), Ruido.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Modos de Comunicación: Verbal y No Verbal 💬 gestures</h4>
                            <p class="mb-2">No solo hablamos con palabras; también comunicamos con nuestro cuerpo y otras señales.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Comunicación Verbal:</strong> Uso de palabras (habladas o escritas).</li>
                            <li><strong>Comunicación No Verbal:</strong> Mensajes sin palabras (gestos, expresiones faciales, postura, contacto visual, tono de voz, proxémica, apariencia).</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Habilidades de Recepción: Escucha Activa 👂</h4>
                            <p class="mb-2">Saber escuchar de manera consciente y empática para comprender verdaderamente al interlocutor.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Prestar Atención Plena:</strong> Concentrarse completamente en el hablante, evitando distracciones.</li>
                            <li><strong>Comprender y Clarificar:</strong> Entender el mensaje y hacer preguntas para clarificar.</li>
                            <li><strong>Empatía y No Juicio:</strong> Ponerse en el lugar del otro y suspender el juicio.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Dominar estos conceptos básicos te ayudará a mejorar tus interacciones diarias. ¡La práctica constante es la clave! 🌟</p>
                    `,
                    questions: [
                        { id: 'c_b_q1', text: 'Menciona un elemento clave del proceso de comunicación.', type: 'text', answer: 'Emisor' },
                        { id: 'c_b_q2', text: '¿Es el lenguaje corporal un tipo de comunicación verbal o no verbal?', type: 'text', answer: 'No verbal' },
                        { id: 'c_b_q3', text: '¿Qué significa "parafrasear" en la escucha activa?', type: 'text', answer: 'Repetir con tus propias palabras lo que la otra persona ha dicho para confirmar que la has entendido.' },
                        { id: 'c_b_q4', text: 'Da un ejemplo de comunicación verbal escrita.', type: 'text', answer: 'Correo electrónico' },
                        { id: 'c_b_q5', text: '¿Por qué es importante la retroalimentación en la comunicación?', type: 'text', answer: 'Para saber si el mensaje fue comprendido y ajustar la comunicación si es necesario.' },
                        { id: 'c_b_q6', text: '¿Cuál es el propósito principal de la comunicación?', type: 'text', answer: 'Intercambiar información, ideas, sentimientos.' },
                        { id: 'c_b_q7', text: 'Si alguien te sonríe, ¿qué tipo de comunicación es?', type: 'text', answer: 'No verbal' },
                        { id: 'c_b_q8', text: 'Menciona un ejemplo de canal de comunicación.', type: 'text', answer: 'Teléfono' },
                        { id: 'c_b_q9', text: '¿Qué es el código en el proceso de comunicación?', type: 'text', answer: 'El sistema de signos compartido, como el idioma.' },
                        { id: 'c_b_q10', text: '¿Qué es el contexto en la comunicación?', type: 'text', answer: 'Las circunstancias que rodean la comunicación.' },
                        { id: 'c_b_q11', text: '¿Qué habilidad de recepción es fundamental para entender a los demás?', type: 'text', answer: 'Escucha activa' },
                        { id: 'c_b_q12', text: 'Si un mensaje no se entiende, ¿qué elemento de la comunicación falló?', type: 'text', answer: 'Mensaje o código' },
                        { id: 'c_b_q13', text: '¿Qué significa mostrar interés genuino al escuchar?', type: 'text', answer: 'Asentir, mantener contacto visual.' },
                        { id: 'c_b_q14', text: 'Da un ejemplo de un saludo informal.', type: 'text', answer: 'Hola' },
                        { id: 'c_b_q15', text: '¿Por qué es importante no interrumpir al escuchar?', type: 'text', answer: 'Para permitir que la otra persona se exprese completamente.' },
                        { id: 'c_b_q16', text: '¿Qué tipo de comunicación es un discurso?', type: 'text', answer: 'Verbal oral' },
                        { id: 'c_b_q17', text: '¿Cuál es la diferencia entre emisor y receptor?', type: 'text', answer: 'El emisor envía el mensaje, el receptor lo recibe.' },
                        { id: 'c_b_q18', text: '¿Qué función cumple el tono de voz en la comunicación no verbal?', type: 'text', answer: 'Transmite emociones o intenciones.' },
                        { id: 'c_b_q19', text: 'Si alguien cruza los brazos, ¿qué podría comunicar no verbalmente?', type: 'text', answer: 'Defensa o incomodidad.' },
                        { id: 'c_b_q20', text: '¿Qué es la comunicación bidireccional?', type: 'text', answer: 'Cuando el emisor y el receptor intercambian roles.' },
                    ]
                },
                intermedio: {
                    theory: `
                        <p class="mb-6">En el nivel Intermedio de Comunicación 🤝, explorarás cómo identificar y superar las barreras que dificultan el mensaje, aprenderás a expresar tus ideas con asertividad y adaptarás tu estilo comunicativo a diversos contextos.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Barreras de la Comunicación 🚧</h4>
                            <p class="mb-2">Obstáculos que interfieren con la claridad de los mensajes.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Ruido Físico:</strong> Sonidos externos y distracciones ambientales.</li>
                            <li><strong>Barreras Semánticas:</strong> Diferencias en el significado de palabras o jergas.</li>
                            <li><strong>Barreras Psicológicas:</strong> Prejuicios, emociones o falta de atención.</li>
                            <li><strong>Barreras Fisiológicas:</strong> Problemas de audición, visión o dicción.</li>
                            <li><strong>Barreras Administrativas:</strong> Problemas de estructura organizacional o sobrecarga de información.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Comunicación Asertiva ✅</h4>
                            <p class="mb-2">Expresar opiniones, necesidades y deseos de forma clara, directa y respetuosa.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Decir "No" Constructivamente:</strong> Establecer límites educada pero firmemente.</li>
                            <li><strong>Expresar Sentimientos y Necesidades:</strong> Usar "yo siento..." para evitar culpar.</li>
                            <li><strong>Negociación Básica:</strong> Buscar soluciones "ganar-ganar".</li>
                            <li><strong>Recibir y Dar Retroalimentación:</strong> Aceptar y dar críticas constructivas.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Adaptación Comunicativa 🌍</h4>
                            <p class="mb-2">Adaptar el estilo de comunicación según la situación y la audiencia.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Formal vs. Informal:</strong> Ajustar lenguaje y tono al contexto.</li>
                            <li><strong>Comunicación Digital y Netiqueta:</strong> Reglas de etiqueta para plataformas digitales.</li>
                            <li><strong>Comunicación en Público (Básico):</strong> Primeros pasos para hablar frente a una audiencia.</li>
                            <li><strong>Comunicación Escrita Efectiva:</strong> Estructurar ideas, usar lenguaje preciso y conciso.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Este nivel te proporcionará las herramientas para comunicarte con mayor confianza y eficacia. ¡Sigue practicando! 🗣️</p>
                    `,
                    questions: [
                        { id: 'c_i_q1', text: 'Menciona una barrera psicológica de la comunicación.', type: 'text', answer: 'Prejuicios' },
                        { id: 'c_i_q2', text: 'Reescribe la frase "Siempre llegas tarde" de forma asertiva.', type: 'text', answer: 'Me siento frustrado/a cuando llegas tarde.' },
                        { id: 'c_i_q3', text: '¿Qué es la netiqueta?', type: 'text', answer: 'Reglas de etiqueta para la comunicación digital.' },
                        { id: 'c_i_q4', text: 'Da un ejemplo de cómo adaptarías tu lenguaje de una situación formal a una informal.', type: 'text', answer: 'De "Estimado Sr. Pérez" a "Hola, Juan".' },
                        { id: 'c_i_q5', text: '¿Qué tipo de comunicación es un gesto con la mano?', type: 'text', answer: 'No verbal' },
                        { id: 'c_i_q6', text: '¿Qué tipo de barrera es un acento fuerte que dificulta la comprensión?', type: 'text', answer: 'Semántica' },
                        { id: 'c_i_q7', text: '¿Qué implica decir "no" de forma constructiva?', type: 'text', answer: 'Establecer límites de manera educada pero firme.' },
                        { id: 'c_i_q8', text: '¿Por qué es importante evitar las mayúsculas en correos electrónicos?', type: 'text', answer: 'Se percibe como gritar.' },
                        { id: 'c_i_q9', text: 'Menciona una técnica de negociación básica.', type: 'text', answer: 'Buscar soluciones donde ambas partes ganen.' },
                        { id: 'c_i_q10', text: '¿Qué se debe considerar al comunicarse en público por primera vez?', type: 'text', answer: 'Contacto visual, postura, modulación de voz.' },
                        { id: 'c_i_q11', text: '¿Cuál es la diferencia entre comunicación pasiva y asertiva?', type: 'text', answer: 'Pasiva evita expresar, asertiva expresa con respeto.' },
                        { id: 'c_i_q12', text: 'Da un ejemplo de barrera física en la comunicación.', type: 'text', answer: 'Música alta.' },
                        { id: 'c_i_q13', text: '¿Cómo puedes expresar tus sentimientos de manera asertiva?', type: 'text', answer: 'Usando "yo siento..."' },
                        { id: 'c_i_q14', text: '¿Qué es la comunicación formal?', type: 'text', answer: 'Lenguaje respetuoso en entornos profesionales.' },
                        { id: 'c_i_q15', text: '¿Qué significa "win-win" en negociación?', type: 'text', answer: 'Que ambas partes ganan.' },
                        { id: 'c_i_q16', text: '¿Qué tipo de barrera es un problema de audición?', type: 'text', answer: 'Fisiológica' },
                        { id: 'c_i_q17', text: '¿Por qué es importante la brevedad en la comunicación digital?', type: 'text', answer: 'Para ser claro y eficiente.' },
                        { id: 'c_i_q18', text: 'Menciona una ventaja de la comunicación asertiva.', type: 'text', answer: 'Mejora las relaciones interpersonales.' },
                        { id: 'c_i_q19', text: '¿Qué es la comunicación informal?', type: 'text', answer: 'Lenguaje coloquial con amigos y familiares.' },
                        { id: 'c_i_q20', text: '¿Qué es la modulación de voz en la comunicación oral?', type: 'text', answer: 'Variar el tono, volumen y velocidad al hablar.' },
                    ]
                },
                avanzado: {
                    theory: `
                        <p class="mb-6">En el nivel Avanzado de Comunicación 🚀, te enfocarás en la comunicación estratégica, la persuasión ética y el liderazgo a través de la palabra.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Comunicación Persuasiva y Retórica 🎯</h4>
                            <p class="mb-2">Aprender a influir en los demás de manera ética y efectiva.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Principios de la Retórica:</strong> Logos (lógica), Ethos (credibilidad), Pathos (emoción).</li>
                            <li><strong>Técnicas de Negociación Avanzadas:</strong> Estrategias para acuerdos complejos y manejo de objeciones.</li>
                            <li><strong>Presentaciones de Alto Impacto:</strong> Diseñar y entregar discursos que cautiven e inspiren.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Comunicación Intercultural y Global 🌐</h4>
                            <p class="mb-2">Entender y adaptarse a las diferencias culturales en la comunicación.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Dimensiones Culturales:</strong> Modelos (Hofstede, Hall) que explican variaciones culturales.</li>
                            <li><strong>Sensibilidad Cultural y Adaptación:</strong> Observar, interpretar y respetar normas de otras culturas.</li>
                            <li><strong>Comunicación en Equipos Globales:</strong> Estrategias para equipos distribuidos culturalmente.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Liderazgo, Gestión y Comunicación de Crisis 🚨</h4>
                            <p class="mb-2">La comunicación como herramienta vital para el liderazgo y la gestión de situaciones difíciles.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Comunicación de Liderazgo:</strong> Inspirar y guiar equipos con comunicación clara y empática.</li>
                            <li><strong>Gestión de Crisis:</strong> Desarrollar planes para comunicar eficazmente en emergencias.</li>
                            <li><strong>Comunicación de Cambio:</strong> Liderar a través del cambio organizacional.</li>
                            <li><strong>Comunicación de Influencia y Redes:</strong> Construir redes profesionales e influir en stakeholders.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Este nivel te transformará en un comunicador maestro, capaz de liderar, persuadir y construir puentes. ¡Tu voz tiene un poder transformador! 🌟</p>
                    `,
                    questions: [
                        { id: 'c_a_q1', text: 'Menciona los tres principios de la retórica de Aristóteles.', type: 'text', answer: 'Logos, Ethos, Pathos' },
                        { id: 'c_a_q2', text: '¿Qué es una cultura de "alto contexto" en comunicación intercultural?', type: 'text', answer: 'Una cultura donde gran parte del significado del mensaje se deriva del contexto no verbal y la situación, no solo de las palabras explícitas.' },
                        { id: 'c_a_q3', text: '¿Por qué es crucial la transparencia en la comunicación de crisis?', type: 'text', answer: 'Para mantener la confianza de los stakeholders y evitar especulaciones o rumores.' },
                        { id: 'c_a_q4', text: 'Da un ejemplo de cómo un líder puede usar la comunicación para inspirar a su equipo.', type: 'text', answer: 'Compartiendo una visión clara y apasionada del futuro, y reconociendo el esfuerzo de cada miembro.' },
                        { id: 'c_a_q5', text: '¿Qué es el storytelling en presentaciones de alto impacto?', type: 'text', answer: 'El uso de narrativas o historias para conectar emocionalmente con la audiencia y hacer el mensaje más memorable.' },
                        { id: 'c_a_q6', text: '¿Cuál es el objetivo principal de la comunicación persuasiva?', type: 'text', answer: 'Influir en los demás de manera ética para lograr objetivos.' },
                        { id: 'c_a_q7', text: '¿Qué significa "Ethos" en retórica?', type: 'text', answer: 'Establecer credibilidad o autoridad.' },
                        { id: 'c_a_q8', text: 'Menciona una dimensión cultural según Hofstede.', type: 'text', answer: 'Individualismo vs. Colectivismo' },
                        { id: 'c_a_q9', text: '¿Qué se busca al manejar objeciones en una negociación avanzada?', type: 'text', answer: 'Soluciones constructivas y valor mutual.' },
                        { id: 'c_a_q10', text: '¿Por qué es importante la empatía en la comunicación de liderazgo?', type: 'text', answer: 'Para conectar con el equipo y fomentar el compromiso.' },
                        { id: 'c_a_q11', text: '¿Qué es una "cultura de bajo contexto"?', type: 'text', answer: 'Una cultura donde la comunicación es explícita y directa, con poco significado derivado del contexto no verbal.' },
                        { id: 'c_a_q12', text: 'Menciona una técnica para hacer presentaciones de alto impacto.', type: 'text', answer: 'Storytelling.' },
                        { id: 'c_a_q13', text: '¿Qué es el "Pathos" en retórica?', type: 'text', answer: 'Apelar a las emociones de la audiencia.' },
                        { id: 'c_a_q14', text: '¿Qué es la comunicación estratégica?', type: 'text', answer: 'Comunicación planificada para lograr objetivos específicos.' },
                        { id: 'c_a_q15', text: '¿Por qué es importante la adaptabilidad cultural en la comunicación global?', type: 'text', answer: 'Para evitar malentendidos y construir relaciones sólidas.' },
                        { id: 'c_a_q16', text: 'Menciona un paso clave en la gestión de crisis.', type: 'text', answer: 'Desarrollar planes y estrategias de comunicación.' },
                        { id: 'c_a_q17', text: '¿Qué significa "Logos" en retórica?', type: 'text', answer: 'Uso de argumentos lógicos, datos y hechos.' },
                        { id: 'c_a_q18', text: '¿Qué es el etnocentrismo en comunicación intercultural?', type: 'text', answer: 'Juzgar otras culturas basándose en los propios estándares culturales.' },
                        { id: 'c_a_q19', text: '¿Cómo ayuda la comunicación a fomentar la resiliencia en un equipo?', type: 'text', answer: 'Al mantener la cohesión y el compromiso.' },
                        { id: 'c_a_q20', text: '¿Qué es el "rapport" en negociación?', type: 'text', answer: 'La relación de confianza y comprensión mutua entre las partes.' },
                    ]
                },
            },
            ingles: {
                basico: {
                    theory: `
                        <p class="mb-6">En el nivel Básico de Inglés 🇬🇧🇺🇸, darás tus primeros pasos para construir una base sólida en el idioma, enfocándote en la comunicación esencial para el día a día.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Fundamentos de Comunicación Oral y Escrita 👋</h4>
                            <p class="mb-2">Aprenderás las frases básicas para interactuar en inglés.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Saludos y Despedidas:</strong> Frases comunes para iniciar y finalizar interacciones.</li>
                            <li><strong>Presentación Personal:</strong> Cómo presentarte a ti mismo y a otros.</li>
                            <li><strong>Preguntas Básicas:</strong> Formular y responder preguntas sencillas sobre información personal.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Vocabulario Esencial y Comprensión Simple 📖</h4>
                            <p class="mb-2">Conocerás palabras y frases comunes para describir tu entorno y entender instrucciones básicas.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Números:</strong> Contar del 1 al 100 y más allá.</li>
                            <li><strong>Colores:</strong> Nombres de colores básicos.</li>
                            <li><strong>Días de la Semana y Meses:</strong> Aprender los días y los meses.</li>
                            <li><strong>Objetos Comunes y Verbos de Acción:</strong> Vocabulario de objetos cotidianos y verbos simples.</li>
                            <li><strong>Comprensión Auditiva Básica:</strong> Entender frases simples y conversaciones lentas.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Estructuras Gramaticales Básicas 🏗️</h4>
                            <p class="mb-2">Introducción a las estructuras más simples del inglés para formar oraciones coherentes.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Verbo "To Be":</strong> Uso de "am", "is", "are" para describir estados y características.</li>
                            <li><strong>Pronombres Personales:</strong> Aprender "I", "you", "he", "she", "it", "we", "they".</li>
                            <li><strong>Artículos:</strong> Uso de "a", "an", "the".</li>
                            <li><strong>Formación de Oraciones Simples:</strong> Estructura Sujeto + Verbo + Complemento (SVO).</li>
                            <li><strong>Adjetivos y Adverbios Básicos:</strong> Introducción a describir sustantivos y verbos.</li>
                            </ul>
                        </div>
                        <p class="mt-4">La clave en este nivel es la práctica constante y la inmersión. ¡No tengas miedo de cometer errores! 😊</p>
                    `,
                    questions: [
                        { id: 'i_b_q1', text: 'Completa: "Hello, my name ___ John."', type: 'text', answer: 'is' },
                        { id: 'i_b_q2', text: 'Traduce "Buenos días" al inglés.', type: 'text', answer: 'Good morning' },
                        { id: 'i_b_q3', text: 'Escribe el número 15 en inglés.', type: 'text', answer: 'fifteen' },
                        { id: 'i_b_q4', text: 'Completa con "a" o "an": "___ orange".', type: 'text', answer: 'an' },
                        { id: 'i_b_q5', text: '¿Cuál es el pronombre personal para referirse a una mujer?', type: 'text', answer: 'she' },
                        { id: 'i_b_q6', text: 'Traduce "Gracias" al inglés.', type: 'text', answer: 'Thank you' },
                        { id: 'i_b_q7', text: '¿Cómo se dice "rojo" en inglés?', type: 'text', answer: 'red' },
                        { id: 'i_b_q8', text: 'Escribe el día de la semana que sigue a "Tuesday".', type: 'text', answer: 'Wednesday' },
                        { id: 'i_b_q9', text: '¿Cómo se dice "libro" en inglés?', type: 'text', answer: 'book' },
                        { id: 'i_b_q10', text: 'Completa: "I ___ from Spain."', type: 'text', answer: 'am' },
                        { id: 'i_b_q11', text: 'Traduce "Adiós" al inglés.', type: 'text', answer: 'Goodbye' },
                        { id: 'i_b_q12', text: 'Escribe el número 50 en inglés.', type: 'text', answer: 'fifty' },
                        { id: 'i_b_q13', text: '¿Cómo se dice "azul" en inglés?', type: 'text', answer: 'blue' },
                        { id: 'i_b_q14', text: 'Escribe el mes que sigue a "March".', type: 'text', answer: 'April' },
                        { id: 'i_b_q15', text: '¿Cómo se dice "comer" en inglés?', type: 'text', answer: 'eat' },
                        { id: 'i_b_q16', text: 'Completa: "They ___ students."', type: 'text', answer: 'are' },
                        { id: 'i_b_q17', text: 'Traduce "¿Cómo estás?" al inglés.', type: 'text', answer: 'How are you?' },
                        { id: 'i_b_q18', text: 'Escribe el número 100 en inglés.', type: 'text', answer: 'one hundred' },
                        { id: 'i_b_q19', text: '¿Cómo se dice "verde" en inglés?', type: 'text', answer: 'green' },
                        { id: 'i_b_q20', text: 'Escribe el día de la semana que precede a "Sunday".', type: 'text', answer: 'Saturday' },
                    ]
                },
                intermedio: {
                    theory: `
                        <p class="mb-6">En el nivel Intermedio de Inglés 🗣️📚, expandirás tu vocabulario, dominarás tiempos verbales más complejos y mejorarás tu fluidez.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Dominio de Tiempos Verbales Clave ⏰</h4>
                            <p class="mb-2">Expresar acciones en diferentes momentos y con distintos matices.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Presente Continuo:</strong> Acciones ocurriendo ahora o planes futuros.</li>
                            <li><strong>Pasado Simple:</strong> Acciones terminadas en el pasado.</li>
                            <li><strong>Futuro Simple (Will / Be Going To):</strong> Planes, predicciones y decisiones espontáneas.</li>
                            <li><strong>Presente Perfecto:</strong> Acciones que comenzaron en el pasado y continúan o tienen relevancia en el presente.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Vocabulario Funcional y Expresiones Comunes 💬</h4>
                            <p class="mb-2">Ampliar tu léxico para hablar sobre temas cotidianos y entender expresiones idiomáticas.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Vocabulario de Viajes:</strong> Términos esenciales para aeropuertos, hoteles, direcciones.</li>
                            <li><strong>Compras y Servicios:</strong> Preguntar precios, tallas, describir productos y solicitar servicios.</li>
                            <li><strong>Salud y Bienestar:</strong> Vocabulario para describir síntomas y hablar con el médico.</li>
                            <li><strong>Expresiones Idiomáticas Básicas:</strong> Frases comunes cuyo significado no es literal.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Mejora de Habilidades Comunicativas Integradas 👂✍️</h4>
                            <p class="mb-2">Enfocarse en la comprensión y producción del idioma de manera más fluida y natural.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Comprensión Auditiva:</strong> Escuchar podcasts, noticias, canciones para mejorar la comprensión.</li>
                            <li><strong>Expresión Oral:</strong> Participar en debates simples, describir experiencias, practicar fluidez y pronunciación.</li>
                            <li><strong>Expresión Escrita:</strong> Redactar correos, descripciones, historias cortas.</li>
                            <li><strong>Comprensión Lectora:</strong> Leer artículos, blogs, historias cortas para entender ideas y vocabulario.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Este nivel te permitirá interactuar con mayor confianza y autonomía. ¡Sigue practicando! 🌊</p>
                    `,
                    questions: [
                        { id: 'i_i_q1', text: 'Conjuga el verbo "to eat" en pasado simple.', type: 'text', answer: 'ate' },
                        { id: 'i_i_q2', text: 'Escribe una oración en presente perfecto usando "have".', type: 'text', answer: 'I have finished my homework.' }, // Allow variations
                        { id: 'i_i_q3', text: 'Imagina que estás de viaje. ¿Cómo preguntarías por el precio de algo?', type: 'text', answer: 'How much is this?' },
                        { id: 'i_i_q4', text: '¿Qué significa la expresión "it\'s raining cats and dogs"?', type: 'text', answer: 'Está lloviendo a cántaros.' },
                        { id: 'i_i_q5', text: 'Menciona una forma de mejorar tu comprensión auditiva en inglés.', type: 'text', answer: 'Escuchar podcasts' },
                        { id: 'i_i_q6', text: 'Completa la oración en presente continuo: "She ___ (study) for the exam."', type: 'text', answer: 'is studying' },
                        { id: 'i_i_q7', text: 'Conjuga el verbo "to go" en pasado simple.', type: 'text', answer: 'went' },
                        { id: 'i_i_q8', text: '¿Cuál es la diferencia entre "will" y "be going to" para el futuro?', type: 'text', answer: '"Will" para predicciones/decisiones espontáneas, "be going to" para planes decididos.' },
                        { id: 'i_i_q9', text: '¿Cómo se dice "aeropuerto" en inglés?', type: 'text', answer: 'airport' },
                        { id: 'i_i_q10', text: '¿Qué significa la expresión "break a leg"?', type: 'text', answer: '¡Buena suerte!' },
                        { id: 'i_i_q11', text: 'Escribe una oración en pasado simple con un verbo irregular.', type: 'text', answer: 'I saw a movie yesterday.' },
                        { id: 'i_i_q12', text: 'Completa: "They have ___ (live) here for ten years."', type: 'text', answer: 'lived' },
                        { id: 'i_i_q13', text: '¿Cómo pedirías la cuenta en un restaurante en inglés?', type: 'text', answer: 'Can I have the bill, please?' },
                        { id: 'i_i_q14', text: '¿Qué significa "a piece of cake"?', type: 'text', answer: 'Muy fácil.' },
                        { id: 'i_i_q15', text: 'Menciona una habilidad clave para la expresión oral en inglés.', type: 'text', answer: 'Fluidez' },
                        { id: 'i_i_q16', text: 'Completa la oración en futuro simple: "I think it ___ (rain) tomorrow."', type: 'text', answer: 'will rain' },
                        { id: 'i_i_q17', text: '¿Cómo se dice "tengo dolor de cabeza" en inglés?', type: 'text', answer: 'I have a headache.' },
                        { id: 'i_i_q18', text: 'Escribe una oración en presente perfecto con "never".', type: 'text', answer: 'I have never been to Paris.' },
                        { id: 'i_i_q19', text: '¿Qué tipo de textos son buenos para practicar la comprensión lectora intermedia?', type: 'text', answer: 'Artículos de noticias sencillos.' },
                        { id: 'i_i_q20', text: '¿Cómo se dice "hotel" en inglés?', type: 'text', answer: 'hotel' },
                    ]
                },
                avanzado: {
                    theory: `
                        <p class="mb-6">En el nivel Avanzado de Inglés 🚀✨, refinarás tu fluidez, precisión y comprensión de matices culturales, preparándote para contextos académicos y profesionales exigentes.</p>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">1. Gramática Avanzada y Estructuras Complejas 🧩</h4>
                            <p class="mb-2">Dominarás las estructuras que dan sofisticación y precisión a tu inglés.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Voz Pasiva:</strong> Uso para enfatizar la acción o el objeto.</li>
                            <li><strong>Discurso Indirecto:</strong> Reportar lo que alguien dijo o preguntó sin citar textualmente.</li>
                            <li><strong>Condicionales Mixtos e Inversiones:</strong> Estructuras avanzadas para expresar hipótesis y énfasis.</li>
                            <li><strong>Frases Nominales y Adjetivales:</strong> Construir oraciones más densas y concisas.</li>
                            <li><strong>Conjunciones y Conectores Avanzados:</strong> Unir ideas de forma lógica y fluida.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">2. Vocabulario Académico y Profesional 💼🎓</h4>
                            <p class="mb-2">Adquirirás el léxico necesario para entornos especializados.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Jerga de Negocios:</strong> Términos específicos de finanzas, marketing, gestión.</li>
                            <li><strong>Vocabulario Académico:</strong> Palabras formales para ensayos, investigaciones y presentaciones.</li>
                            <li><strong>Sinónimos y Antónimos Avanzados:</strong> Expresar ideas con mayor precisión y variedad.</li>
                            <li><strong>Phrasal Verbs y Collocations:</strong> Dominar combinaciones de palabras esenciales para la fluidez.</li>
                            <li><strong>Modismos y Expresiones Idiomáticas:</strong> Comprender y usar expresiones culturales no literales.</li>
                            </ul>
                        </div>
                        <div class="bg-blue-50 p-6 rounded-lg mb-6 shadow-sm">
                            <h4 class="text-blue-800 text-xl font-semibold mb-3">3. Fluidez, Precisión y Comprensión Cultural 🌐🗣️</h4>
                            <p class="mb-2">Perfeccionarás tu capacidad para comunicarte con naturalidad y entender las sutilezas culturales.</p>
                            <ul class="list-disc list-inside ml-4 mb-4">
                            <li><strong>Pronunciación Avanzada:</strong> Refinar entonación, ritmo, reducciones y "linking sounds".</li>
                            <li><strong>Comprensión de Acentos:</strong> Exposición y práctica con diferentes acentos nativos.</li>
                            <li><strong>Debate y Argumentación:</strong> Participar en discusiones complejas, defender puntos de vista.</li>
                            <li><strong>Análisis Crítico de Textos:</strong> Leer y comprender textos complejos, identificando tono y propósito.</li>
                            <li><strong>Escritura Avanzada:</strong> Redactar ensayos persuasivos, informes técnicos y correspondencia profesional.</li>
                            </ul>
                        </div>
                        <p class="mt-4">Este nivel te permitirá usar el inglés con maestría, abriendo un mundo de oportunidades. ¡El mundo es tu escenario y el inglés tu pasaporte! 🌟</p>
                    `,
                    questions: [
                        { id: 'i_a_q1', text: 'Transforma la siguiente oración a voz pasiva: "The company launched a new product."', type: 'text', answer: 'A new product was launched by the company.' },
                        { id: 'i_a_q2', text: 'Reporta la siguiente pregunta: "Are you coming to the meeting tomorrow?"', type: 'text', answer: 'He asked if I was coming to the meeting the next day.' },
                        { id: 'i_a_q3', text: '¿Qué significa el phrasal verb "put off"?', type: 'text', answer: 'Posponer' },
                        { id: 'i_a_q4', text: 'Menciona un sinónimo avanzado para "good".', type: 'text', answer: 'Excellent' },
                        { id: 'i_a_q5', text: '¿Qué es una "collocation" en inglés?', type: 'text', answer: 'Una combinación de palabras que ocurre frecuentemente de forma natural.' },
                        { id: 'i_a_q6', text: 'Transforma a voz pasiva: "They built a new bridge."', type: 'text', answer: 'A new bridge was built by them.' },
                        { id: 'i_a_q7', text: 'Reporta la siguiente afirmación: "I have finished my project."', type: 'text', answer: 'She said she had finished her project.' },
                        { id: 'i_a_q8', text: '¿Cuál es la diferencia entre un condicional tipo 2 y un condicional mixto?', type: 'text', answer: 'El tipo 2 se refiere a situaciones hipotéticas en el presente/futuro, el mixto combina pasado y presente.' },
                        { id: 'i_a_q9', text: 'Menciona un término de jerga de negocios.', type: 'text', answer: 'ROI' },
                        { id: 'i_a_q10', text: '¿Qué significa el phrasal verb "take off"?', type: 'text', answer: 'Despegar' },
                        { id: 'i_a_q11', text: 'Menciona un antónimo avanzado para "happy".', type: 'text', answer: 'Miserable' },
                        { id: 'i_a_q12', text: '¿Qué es la inversión en gramática inglesa?', type: 'text', answer: 'Cambiar el orden sujeto-verbo para énfasis o formalidad.' },
                        { id: 'i_a_q13', text: '¿Cómo se dice "recursos humanos" en inglés?', type: 'text', answer: 'Human Resources' },
                        { id: 'i_a_q14', text: '¿Qué es el "linking sounds" en pronunciación avanzada?', type: 'text', answer: 'La unión de sonidos entre palabras.' },
                        { id: 'i_a_q15', text: 'Menciona un beneficio de leer artículos científicos en inglés.', type: 'text', answer: 'Mejora el vocabulario académico.' },
                        { id: 'i_a_q16', text: 'Transforma a voz pasiva: "The students are writing essays."', type: 'text', answer: 'Essays are being written by the students.' },
                        { id: 'i_a_q17', text: 'Reporta la siguiente pregunta: "What did you do yesterday?"', type: 'text', answer: 'He asked what I had done the day before.' },
                        { id: 'i_a_q18', text: '¿Qué significa el phrasal verb "get along with"?', type: 'text', answer: 'Llevarse bien con alguien.' },
                        { id: 'i_a_q19', text: 'Menciona un sinónimo avanzado para "important".', type: 'text', answer: 'Crucial' },
                        { id: 'i_a_q20', text: 'Da un ejemplo de "collocation" con el verbo "make".', type: 'text', answer: 'Make a decision.' },
                    ]
                },
            },
        };

        const renderQuestionSection = (questions) => {
            userAnswers = {};
            score = 0;

            const questionSectionDiv = document.createElement('div');
            questionSectionDiv.className = "bg-blue-50 p-8 rounded-2xl text-gray-800 border-2 border-blue-200 text-lg leading-relaxed mt-8 shadow-inner";
            questionSectionDiv.innerHTML = `
                <h3 class="text-2xl font-bold text-blue-800 mb-6">Ejercicios - ¡Pon a prueba tus conocimientos! 📝</h3>
                <p class="mb-6 text-gray-700">Responde las siguientes preguntas.</p>
                <ol class="list-decimal list-inside ml-4 space-y-6" id="questions-list"></ol>
                <button id="check-answers-btn" class="mt-8 px-8 py-4 bg-blue-600 text-white rounded-full shadow-lg hover:bg-blue-700 transition-colors duration-300 text-xl font-bold block mx-auto transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-blue-300">
                    Verificar Respuestas ✨
                </button>
                <div id="results-display" class="mt-8 text-center text-2xl font-bold hidden"></div>
            `;

            const questionsList = questionSectionDiv.querySelector('#questions-list');
            questions.forEach((q, index) => {
                const listItem = document.createElement('li');
                listItem.className = "mb-4";
                listItem.innerHTML = `
                    <p class="font-semibold mb-2 text-gray-900">${index + 1}. ${q.text}</p>
                    <input
                        type="${q.type === 'number' ? 'number' : 'text'}"
                        id="q-${q.id}"
                        class="w-full p-3 rounded-lg border-2 focus:outline-none focus:ring-2 border-gray-300 focus:border-blue-500 focus:ring-blue-300 transition-all duration-200"
                        placeholder="Tu respuesta..."
                    />
                    <p id="feedback-${q.id}" class="mt-2 text-sm font-medium hidden"></p>
                `;
                questionsList.appendChild(listItem);

                const inputElement = listItem.querySelector(`#q-${q.id}`);
                inputElement.addEventListener('input', (e) => {
                    userAnswers[q.id] = e.target.value;
                    document.getElementById('results-display').classList.add('hidden');
                    questions.forEach(question => {
                        const feedbackElement = document.getElementById(`feedback-${question.id}`);
                        const inputField = document.getElementById(`q-${question.id}`);
                        if (feedbackElement) {
                            feedbackElement.classList.add('hidden');
                            feedbackElement.classList.remove('feedback-animation');
                        }
                        if (inputField) {
                            inputField.classList.remove('border-green-500', 'focus:ring-green-300', 'border-red-500', 'focus:ring-red-300');
                            inputField.classList.add('border-gray-300', 'focus:border-blue-500', 'focus:ring-blue-300');
                        }
                    });
                });
            });

            questionSectionDiv.querySelector('#check-answers-btn').addEventListener('click', () => {
                let correctCount = 0;
                questions.forEach(q => {
                    const userAnswer = userAnswers[q.id];
                    let isCorrect = false;

                    if (q.type === 'number') {
                        isCorrect = Math.abs(parseFloat(userAnswer) - q.answer) < 0.001;
                    } else if (q.type === 'text') {
                        const correctAnswers = Array.isArray(q.answer) ? q.answer : [q.answer];
                        isCorrect = correctAnswers.some(ans => userAnswer && userAnswer.trim().toLowerCase() === ans.toLowerCase());
                    }

                    if (isCorrect) {
                        correctCount++;
                    }

                    const inputElement = document.getElementById(`q-${q.id}`);
                    const feedbackElement = document.getElementById(`feedback-${q.id}`);

                    inputElement.classList.remove('border-green-500', 'focus:ring-green-300', 'border-red-500', 'focus:ring-red-300', 'border-gray-300', 'focus:border-blue-500', 'focus:ring-blue-300');
                    if (isCorrect) {
                        inputElement.classList.add('border-blue-500', 'focus:ring-blue-300');
                        feedbackElement.textContent = '¡Correcto! ✅';
                        feedbackElement.classList.remove('text-red-600');
                        feedbackElement.classList.add('text-blue-600');
                    } else {
                        inputElement.classList.add('border-red-500', 'focus:ring-red-300');
                        feedbackElement.textContent = `Incorrecto. La respuesta correcta es: ${Array.isArray(q.answer) ? q.answer.join(' / ') : q.answer}`;
                        feedbackElement.classList.remove('text-blue-600');
                        feedbackElement.classList.add('text-red-600');
                    }
                    feedbackElement.classList.remove('hidden');
                    feedbackElement.classList.add('feedback-animation'); // Add animation class
                });

                score = correctCount;

                const resultsDisplay = document.getElementById('results-display');
                resultsDisplay.innerHTML = `
                    <p>Tu puntuación: <span class="text-blue-700">${score}</span> de <span class="text-blue-700">${questions.length}</span></p>
                    ${score === questions.length ? '<p class="text-blue-600 mt-2">¡Excelente trabajo! 🎉</p>' : '<p class="text-orange-600 mt-2">¡Sigue practicando para mejorar! 💪</p>'}
                `;
                resultsDisplay.classList.remove('hidden');
            });

            return questionSectionDiv;
        };

        const renderCourseDetail = (course) => {
            const appDiv = document.getElementById('app');
            appDiv.innerHTML = '';

            // Handle external link for Minijuegos
            if (course.isExternal) {
                window.open(course.url, '_blank');
                selectedCourse = null; // Reset selected course to return to main page
                renderApp();
                return;
            }

            const currentLevelContent = courseContent[course.id][selectedLevel];

            const detailDiv = document.createElement('div');
            detailDiv.className = "bg-white p-10 rounded-3xl shadow-2xl w-full max-w-4xl border border-blue-100";

            const backButton = document.createElement('button');
            backButton.className = "mb-8 px-6 py-3 bg-gradient-to-r from-sky-500 to-blue-600 text-white rounded-full shadow-lg hover:shadow-xl hover:from-sky-600 hover:to-blue-700 transition-all duration-300 flex items-center text-lg font-semibold transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-sky-300";
            backButton.innerHTML = `
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-3" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z" clip-rule="evenodd" />
                </svg>
                Volver a Cursos
            `;
            backButton.addEventListener('click', () => {
                selectedCourse = null;
                renderApp();
            });
            detailDiv.appendChild(backButton);

            const title = document.createElement('h2');
            title.className = "text-5xl font-extrabold text-blue-900 mb-6 leading-tight gradient-text from-blue-700 to-indigo-700 shadow-text-dark";
            title.textContent = course.name;
            detailDiv.appendChild(title);

            const welcomeText = document.createElement('p');
            welcomeText.className = "text-gray-700 text-xl mb-8 leading-relaxed";
            welcomeText.textContent = `¡Bienvenido al curso de ${course.name}! Selecciona un nivel para comenzar tu aprendizaje.`;
            detailDiv.appendChild(welcomeText);

            const levelButtonsContainer = document.createElement('div');
            levelButtonsContainer.className = "mb-8 flex justify-center space-x-4";
            ['basico', 'intermedio', 'avanzado'].forEach(level => {
                const button = document.createElement('button');
                button.textContent = level.charAt(0).toUpperCase() + level.slice(1);
                button.className = `px-6 py-3 rounded-full text-lg font-semibold transition-all duration-200 transform hover:scale-105 focus:outline-none focus:ring-4
                    ${selectedLevel === level
                        ? 'bg-gradient-to-r from-blue-600 to-indigo-700 text-white shadow-md focus:ring-blue-300'
                        : 'bg-gray-200 text-gray-700 hover:bg-blue-200 hover:text-blue-800 focus:ring-gray-300'
                    }`;
                button.addEventListener('click', () => {
                    selectedLevel = level;
                    renderCourseDetail(course);
                });
                levelButtonsContainer.appendChild(button);
            });
            detailDiv.appendChild(levelButtonsContainer);

            const theorySection = document.createElement('div');
            theorySection.className = "bg-blue-50 p-8 rounded-2xl text-gray-800 border-2 border-blue-200 text-lg leading-relaxed theory-content shadow-lg";
            theorySection.innerHTML = `
                <h3 class="text-2xl font-bold text-blue-800 mb-4 gradient-text from-blue-600 to-sky-600 shadow-text-light">Teoría del Curso - Nivel ${selectedLevel.charAt(0).toUpperCase() + selectedLevel.slice(1)}</h3>
                ${currentLevelContent.theory}
            `;
            detailDiv.appendChild(theorySection);

            if (currentLevelContent.questions && currentLevelContent.questions.length > 0) {
                detailDiv.appendChild(renderQuestionSection(currentLevelContent.questions));
            }

            appDiv.appendChild(detailDiv);
        };

        const renderCourseCards = () => {
            const appDiv = document.getElementById('app');
            appDiv.innerHTML = `
                <h1 class="text-6xl font-extrabold text-center text-gray-900 mb-16 drop-shadow-lg leading-tight gradient-text from-blue-600 to-indigo-600 shadow-text-dark">
                    <span class="from-blue-600 to-sky-600">Chisfuerzo:</span> <span class="from-sky-600 to-indigo-600">Aprende</span> y <span class="from-indigo-600 to-purple-600">Crece</span>
                </h1>
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-10" id="course-cards-container"></div>
            `;
            const courseCardsContainer = document.getElementById('course-cards-container');

            courses.forEach(course => {
                const cardDiv = document.createElement('div');
                cardDiv.className = "card-div bg-white p-8 rounded-3xl shadow-xl hover:shadow-2xl transition-all duration-300 cursor-pointer transform hover:-translate-y-2 flex flex-col items-center text-center border-2 border-blue-100 hover:border-blue-300";
                cardDiv.innerHTML = `
                    <div class="card-icon text-blue-700">
                        ${course.id === 'matematicas' ? '➕' : ''}
                        ${course.id === 'comunicacion' ? '🗣️' : ''}
                        ${course.id === 'ingles' ? '🇬🇧' : ''}
                        ${course.id === 'minijuegos' ? '🎮' : ''}
                    </div>
                    <h3 class="text-3xl font-bold text-gray-900 mb-3">${course.name}</h3>
                    <p class="text-gray-600 text-base leading-relaxed">${course.description}</p>
                `;
                cardDiv.addEventListener('click', () => {
                    selectedCourse = course;
                    selectedLevel = 'basico'; // Default to basic, but for external links it won't matter
                    renderApp();
                });
                courseCardsContainer.appendChild(cardDiv);
            });
        };

        const renderApp = () => {
            if (selectedCourse) {
                renderCourseDetail(selectedCourse);
            } else {
                renderCourseCards();
            }
        };

        document.addEventListener('DOMContentLoaded', async () => {
            await initFirebase();
            renderApp();
        });
    </script>
</body>
</html>
