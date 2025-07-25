---
id: overview.md
title: Qué es Milvus
related_key: Milvus Overview
summary: >-
  Milvus es una base de datos vectorial de alto rendimiento y gran escalabilidad
  que se ejecuta eficientemente en una amplia gama de entornos, desde un
  ordenador portátil hasta sistemas distribuidos a gran escala. Está disponible
  como software de código abierto y como servicio en la nube.
---
<h1 id="What-is-Milvus" class="common-anchor-header">¿Qué es Milvus?<button data-href="#What-is-Milvus" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h1><p><span>Milvus <span style="display: inline-block; vertical-align: middle;">
<audio id="milvus-audio" style="display: none;">
<source src="https://en-audio.howtopronounce.com/15783806805e142d8844912.mp3" type="audio/mp3" />
</audio>
<span style="
    display: inline-block;
    width: 20px;
    height: 20px;
    background: url('https://milvus.io/docs/v2.6.x/assets/hearing.png') no-repeat center center;
    background-size: contain;
    cursor: pointer;
    margin-left: 4px;
  " onclick="document.getElementById('milvus-audio').play()"></span>
</span></span> es un ave rapaz del género Milvus de la familia de los halcones Accipaitridae, famosa por su velocidad de vuelo, aguda visión y notable adaptabilidad.</p>
<style>
  audio::-webkit-media-controls { display: none !important; }</style>
<p>Zilliz adopta el nombre de Milvus para su base de datos vectorial de código abierto, altamente escalable y de alto rendimiento, que funciona con eficacia en una amplia gama de entornos, desde un ordenador portátil hasta sistemas distribuidos a gran escala. Está disponible como software de código abierto y como servicio en la nube.</p>
<p>Desarrollado por Zilliz y pronto donado a la LF AI &amp; Data Foundation en el marco de la Linux Foundation, Milvus se ha convertido en uno de los principales proyectos de bases de datos vectoriales de código abierto del mundo. Se distribuye bajo la licencia Apache 2.0, y la mayoría de los colaboradores son expertos de la comunidad de computación de alto rendimiento (HPC), especializados en la construcción de sistemas a gran escala y en la optimización de código compatible con el hardware. Entre los principales colaboradores figuran profesionales de Zilliz, ARM, NVIDIA, AMD, Intel, Meta, IBM, Salesforce, Alibaba y Microsoft.</p>
<p>Curiosamente, todos los proyectos de código abierto de Zilliz llevan el nombre de un pájaro, una convención que simboliza la libertad, la previsión y la evolución ágil de la tecnología.</p>
<h2 id="Unstructured-Data-Embeddings-and-Milvus" class="common-anchor-header">Datos no estructurados, embebidos y Milvus<button data-href="#Unstructured-Data-Embeddings-and-Milvus" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>Los datos no estructurados, como texto, imágenes y audio, varían de formato y contienen una rica semántica subyacente, lo que dificulta su análisis. Para gestionar esta complejidad, se utilizan embebimientos que convierten los datos no estructurados en vectores numéricos que capturan sus características esenciales. A continuación, estos vectores se almacenan en una base de datos vectorial, lo que permite realizar búsquedas y análisis rápidos y escalables.</p>
<p>Milvus ofrece sólidas capacidades de modelado de datos, lo que le permite organizar sus datos no estructurados o multimodales en colecciones estructuradas. Es compatible con una amplia gama de tipos de datos para el modelado de atributos diferentes, incluidos los tipos numéricos y de caracteres comunes, varios tipos de vectores, matrices, conjuntos y JSON, lo que le ahorra el esfuerzo de mantener múltiples sistemas de bases de datos.</p>
<p>
  
   <span class="img-wrapper"> <img translate="no" src="/docs/v2.6.x/assets/unstructured-data-embedding-and-milvus.png" alt="Untructured data, embeddings, and Milvus" class="doc-image" id="untructured-data,-embeddings,-and-milvus" />
   </span> <span class="img-wrapper"> <span>Datos no estructurados, incrustaciones y Milvus</span> </span></p>
<p>Milvus ofrece tres modos de implementación, que cubren una amplia gama de escalas de datos, desde la creación de prototipos locales en Jupyter Notebooks hasta clústeres masivos de Kubernetes que administran decenas de miles de millones de vectores:</p>
<ul>
<li>Milvus Lite es una biblioteca Python que puede integrarse fácilmente en sus aplicaciones. Como versión ligera de Milvus, es ideal para la creación rápida de prototipos en cuadernos Jupyter o para su ejecución en dispositivos periféricos con recursos limitados. <a href="/docs/es/milvus_lite.md">Más información</a>.</li>
<li>Milvus Standalone es una implementación de servidor de una sola máquina, con todos los componentes agrupados en una sola imagen Docker para una implementación conveniente. Obtenga <a href="/docs/es/install_standalone-docker.md">más información</a>.</li>
<li>Milvus Distributed puede desplegarse en clústeres Kubernetes, con una arquitectura nativa de la nube diseñada para escenarios a escala de miles de millones o incluso mayor. Esta arquitectura garantiza la redundancia en componentes críticos. <a href="/docs/es/install_cluster-milvusoperator.md">Más información</a>.</li>
</ul>
<h2 id="What-Makes-Milvus-so-Fast" class="common-anchor-header">Qué hace que Milvus sea tan rápido？<button data-href="#What-Makes-Milvus-so-Fast" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>Milvus fue diseñado desde el primer día para ser un sistema de base de datos vectorial altamente eficiente. En la mayoría de los casos, Milvus supera a otras bases de datos vectoriales entre 2 y 5 veces (véanse los resultados de VectorDBBench). Este alto rendimiento es el resultado de varias decisiones clave de diseño:</p>
<p><strong>Optimización en función del hardware</strong>: Para acomodar Milvus en varios entornos de hardware, hemos optimizado su rendimiento específicamente para muchas arquitecturas y plataformas de hardware, incluyendo AVX512, SIMD, GPUs y SSD NVMe.</p>
<p><strong>Algoritmos de búsqueda avanzados</strong>: Milvus admite una amplia gama de algoritmos de indexación/búsqueda en memoria y en disco, incluidos IVF, HNSW, DiskANN y más, todos los cuales han sido profundamente optimizados. En comparación con implementaciones populares como FAISS y HNSWLib, Milvus ofrece un rendimiento entre un 30% y un 70% superior.</p>
<p><strong>Motor de búsqueda en C++</strong>: Más del 80% del rendimiento de una base de datos vectorial viene determinado por su motor de búsqueda. Milvus utiliza C++ para este componente crítico debido al alto rendimiento del lenguaje, la optimización de bajo nivel y la gestión eficiente de los recursos. Y lo que es más importante, Milvus integra numerosas optimizaciones de código conscientes del hardware, que van desde la vectorización a nivel de ensamblador hasta la paralelización y programación multihilo, para aprovechar al máximo las capacidades del hardware.</p>
<p><strong>Orientado a columnas</strong>: Milvus es un sistema de base de datos vectorial orientado a columnas. Las principales ventajas proceden de los patrones de acceso a los datos. Al realizar consultas, una base de datos orientada a columnas lee sólo los campos específicos implicados en la consulta, en lugar de filas enteras, lo que reduce enormemente la cantidad de datos a los que se accede. Además, las operaciones sobre datos basados en columnas pueden vectorizarse fácilmente, lo que permite aplicar operaciones en las columnas enteras a la vez, mejorando aún más el rendimiento.</p>
<h2 id="What-Makes-Milvus-so-Scalable" class="common-anchor-header">Qué hace que Milvus sea tan escalable<button data-href="#What-Makes-Milvus-so-Scalable" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>En 2022, Milvus soportaba vectores a escala de miles de millones, y en 2023, escaló hasta decenas de miles de millones con una estabilidad constante, alimentando escenarios a gran escala para más de 300 grandes empresas, como Salesforce, PayPal, Shopee, Airbnb, eBay, NVIDIA, IBM, AT&amp;T, LINE, ROBLOX, Inflection, etc.</p>
<p>La arquitectura de sistema altamente desacoplada y nativa de la nube de Milvus garantiza que el sistema pueda expandirse continuamente a medida que crecen los datos:</p>
<p>
  
   <span class="img-wrapper"> <img translate="no" src="/docs/v2.6.x/assets/milvus_architecture_2_6.png" alt="Highly decoupled system architecture of Milvus" class="doc-image" id="highly-decoupled-system-architecture-of-milvus" />
   </span> <span class="img-wrapper"> <span>Arquitectura de sistema altamente desacoplada de Milvus</span> </span></p>
<p>Milvus en sí es totalmente apátrida, por lo que se puede escalar fácilmente con la ayuda de Kubernetes o nubes públicas. Además, los componentes de Milvus están bien desacoplados, con las tres tareas más críticas -búsqueda, inserción de datos e indexación/compactación- diseñadas como procesos fácilmente paralelizables, con la lógica compleja separada. Esto garantiza que el nodo de consulta, el nodo de datos y el nodo de indexación correspondientes puedan ampliarse y reducirse de forma independiente, optimizando el rendimiento y la rentabilidad.</p>
<h2 id="Types-of-Searches-Supported-by-Milvus" class="common-anchor-header">Tipos de búsquedas compatibles con Milvus<button data-href="#Types-of-Searches-Supported-by-Milvus" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>Milvus admite varios tipos de funciones de búsqueda para satisfacer las demandas de diferentes casos de uso:</p>
<ul>
<li><a href="/docs/es/single-vector-search.md#Basic-search">Búsqueda RNA</a>: Encuentra los K vectores más cercanos a su vector de consulta.</li>
<li><a href="/docs/es/single-vector-search.md#Filtered-search">Búsqueda por filtrado</a>: Realiza la búsqueda RNA bajo condiciones de filtrado especificadas.</li>
<li><a href="/docs/es/single-vector-search.md#Range-search">Búsqueda por rango</a>: Busca vectores dentro de un radio especificado a partir del vector de consulta.</li>
<li><a href="/docs/es/multi-vector-search.md">Búsqueda híbrida</a>: Realiza una búsqueda RNA basada en múltiples campos vectoriales.</li>
<li><a href="/docs/es/full-text-search.md">Búsqueda de texto completo</a>: Búsqueda de texto completo basada en BM25.</li>
<li><a href="/docs/es/weighted-ranker.md">Reordenación</a>: Ajusta el orden de los resultados de la búsqueda basándose en criterios adicionales o en un algoritmo secundario, refinando los resultados iniciales de la búsqueda RNA.</li>
<li><a href="/docs/es/get-and-scalar-query.md#Get-Entities-by-ID">Recuperar</a>: Recupera datos por sus claves primarias.</li>
<li><a href="/docs/es/get-and-scalar-query.md#Use-Basic-Operators">Consulta</a>: Recupera datos utilizando expresiones específicas.</li>
</ul>
<h2 id="Comprehensive-Feature-Set" class="common-anchor-header">Completo conjunto de funciones<button data-href="#Comprehensive-Feature-Set" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>Además de las funciones de búsqueda clave mencionadas anteriormente, Milvus también proporciona un conjunto de funciones implementadas en torno a las búsquedas RNA para que pueda utilizar plenamente sus capacidades.</p>
<h3 id="API-and-SDK" class="common-anchor-header">API y SDK</h3><ul>
<li><a href="https://milvus.io/api-reference/restful/v2.4.x/About.md">API RESTful</a> (oficial)</li>
<li><a href="https://milvus.io/api-reference/pymilvus/v2.4.x/About.md">PyMilvus</a> (Python SDK) (oficial)</li>
<li><a href="https://milvus.io/api-reference/go/v2.4.x/About.md">Go SDK</a> (oficial)</li>
<li><a href="https://milvus.io/api-reference/java/v2.4.x/About.md">Java SDK</a> (oficial)</li>
<li>SDK de<a href="https://milvus.io/api-reference/node/v2.4.x/About.md">Node.js</a> (JavaScript) (oficial)</li>
<li><a href="https://milvus.io/api-reference/csharp/v2.2.x/About.md">C#</a> (contribuido por Microsoft)</li>
<li>C++ SDK (en desarrollo)</li>
<li>SDK de Rust (en desarrollo)</li>
</ul>
<h3 id="Advanced-Data-Types" class="common-anchor-header">Tipos de datos avanzados</h3><p>Además de los tipos de datos primitivos, Milvus soporta varios tipos de datos avanzados y sus respectivas métricas de distancia aplicables.</p>
<ul>
<li><a href="/docs/es/sparse_vector.md">Vectores dispersos</a></li>
<li><a href="/docs/es/index-vector-fields.md">Vectores binarios</a></li>
<li><a href="/docs/es/use-json-fields.md">Soporte JSON</a></li>
<li><a href="/docs/es/array_data_type.md">Soporte para matrices</a></li>
<li>Texto (en desarrollo)</li>
<li>Geolocalización (en desarrollo)</li>
</ul>
<h3 id="Why-Milvus" class="common-anchor-header">¿Por qué Milvus?</h3><ul>
<li><p><strong>Alto rendimiento a escala y alta disponibilidad</strong></p>
<p>Milvus presenta una <a href="/docs/es/architecture_overview.md">arquitectura distribuida</a> que separa la <a href="/docs/es/data_processing.md#Data-query">computación</a> del <a href="/docs/es/data_processing.md#Data-insertion">almacenamiento</a>. Milvus puede escalar horizontalmente y adaptarse a diversos patrones de tráfico, logrando un rendimiento óptimo mediante el aumento independiente de los nodos de consulta para la carga de trabajo de lectura pesada y el nodo de datos para la carga de trabajo de escritura pesada. Los microservicios sin estado de K8s permiten <a href="/docs/es/coordinator_ha.md#Coordinator-HA">una rápida recuperación</a> en caso de fallo, lo que garantiza una alta disponibilidad. La compatibilidad con <a href="/docs/es/replica.md">réplicas</a> mejora aún más la tolerancia a fallos y el rendimiento mediante la carga de segmentos de datos en varios nodos de consulta. Consulte la comparativa de <a href="https://zilliz.com/vector-database-benchmark-tool">rendimiento</a>.</p></li>
<li><p><strong>Soporte para varios tipos de índices vectoriales y aceleración por hardware</strong></p>
<p>Milvus separa el sistema y el núcleo del motor de búsqueda vectorial, lo que le permite soportar los principales tipos de índices vectoriales optimizados para diferentes escenarios, incluidos HNSW, IVF, FLAT (fuerza bruta), SCANN y DiskANN, con variaciones <a href="/docs/es/index-explained.md">basadas en la cuantización</a> y <a href="/docs/es/mmap.md">mmap</a>. Milvus optimiza la búsqueda vectorial para funciones avanzadas como <a href="/docs/es/boolean.md">el filtrado de metadatos</a> y la <a href="/docs/es/range-search.md">búsqueda por rangos</a>. Además, Milvus implementa la aceleración por hardware para mejorar el rendimiento de la búsqueda vectorial y admite la indexación por GPU, como <a href="/docs/es/gpu-cagra.md">CAGRA</a> de NVIDIA.</p></li>
<li><p><strong>Arrendamiento múltiple flexible y almacenamiento en caliente/frío</strong></p>
<p>Milvus admite la <a href="/docs/es/multi_tenancy.md#Multi-tenancy-strategies">multitenencia</a> mediante el aislamiento a nivel de base de datos, colección, partición o clave de partición. Las estrategias flexibles permiten que un único clúster gestione de cientos a millones de inquilinos, además de garantizar un rendimiento de búsqueda optimizado y un control de acceso flexible. Milvus mejora la rentabilidad con el almacenamiento en caliente/frío. Los datos calientes a los que se accede con frecuencia pueden almacenarse en memoria o en unidades SSD para mejorar el rendimiento, mientras que los datos fríos a los que se accede con menos frecuencia se guardan en un almacenamiento más lento y rentable. Este mecanismo puede reducir significativamente los costes, manteniendo al mismo tiempo un alto rendimiento para tareas críticas.</p></li>
<li><p><strong>Vector disperso para búsqueda de texto completo y búsqueda híbrida</strong></p>
<p>Además de la búsqueda semántica mediante vectores densos, Milvus también admite de forma nativa la búsqueda de <a href="/docs/es/full-text-search.md">texto completo</a> con BM25, así como la incrustación dispersa aprendida como SPLADE y BGE-M3. Los usuarios pueden almacenar el vector disperso y el vector denso en la misma colección, y definir funciones para jerarquizar los resultados de múltiples peticiones de búsqueda. Vea ejemplos de <a href="/docs/es/full_text_search_with_milvus.md">búsqueda híbrida con búsqueda semántica + búsqueda de texto completo</a>.</p></li>
<li><p><strong>Seguridad de datos y control de acceso de grano fino</strong></p>
<p>Milvus garantiza la seguridad de los datos implementando la <a href="/docs/es/authenticate.md">autenticación obligatoria de usuarios</a>, el <a href="/docs/es/tls.md">cifrado TLS</a> y el <a href="/docs/es/rbac.md">control de acceso basado en roles (RBAC)</a>. La autenticación de usuarios garantiza que sólo los usuarios autorizados con credenciales válidas puedan acceder a la base de datos, mientras que el cifrado TLS protege todas las comunicaciones dentro de la red. Además, RBAC permite un control de acceso detallado mediante la asignación de permisos específicos a los usuarios en función de sus funciones. Estas características hacen de Milvus una opción sólida y segura para las aplicaciones empresariales, protegiendo los datos confidenciales de accesos no autorizados y posibles infracciones.</p></li>
</ul>
<h3 id="AI-Integrations" class="common-anchor-header">Integraciones de IA</h3><ul>
<li><p>Integraciones de modelos de incrustación Los modelos de incrustación convierten los datos no estructurados en su representación numérica en un espacio de datos de alta dimensión para que pueda almacenarlos en Milvus. Actualmente, PyMilvus, el SDK de Python, integra varios modelos de incrustación para que pueda preparar rápidamente sus datos en incrustaciones vectoriales. Para más detalles, consulte <a href="/docs/es/embeddings.md">Visión general de la incrustación</a>.</p></li>
<li><p>Integración de modelos de reordenación En el ámbito de la recuperación de información y la IA generativa, un reordenador es una herramienta esencial que optimiza el orden de los resultados de las búsquedas iniciales. PyMilvus también integra varios modelos de reordenación para optimizar el orden de los resultados obtenidos en las búsquedas iniciales. Para más detalles, consulte <a href="/docs/es/rerankers-overview.md">Visión general de los rerankers</a>.</p></li>
<li><p>LangChain y otras integraciones de herramientas de IA En la era GenAI, las herramientas, como LangChain, reciben mucha atención por parte de los desarrolladores de aplicaciones. Como componente central, Milvus suele servir como almacén de vectores en dichas herramientas. Para saber cómo integrar Milvus en sus herramientas de IA favoritas, consulte nuestras <a href="/docs/es/integrate_with_openai.md">Integraciones</a> y <a href="/docs/es/build-rag-with-milvus.md">tutoriales</a>.</p></li>
</ul>
<h3 id="Tools-and-Ecosystem" class="common-anchor-header">Herramientas y ecosistema</h3><ul>
<li><p>Attu Attu es una GUI intuitiva todo-en-uno que le ayuda a gestionar Milvus y los datos que almacena. Para más detalles, consulte el repositorio de <a href="https://github.com/zilliztech/attu">Attu</a>.</p></li>
<li><p>Birdwatcher Birdwatcher es una herramienta de depuración para Milvus. Usándola para conectarse a etcd, puede comprobar el estado de su sistema Milvus o configurarlo sobre la marcha. Para más detalles, consulte <a href="/docs/es/birdwatcher_overview.md">BirdWatcher</a>.</p></li>
<li><p>Integraciones de Promethus y Grafana Prometheus es un conjunto de herramientas de alerta y supervisión de sistemas de código abierto para Kubernetes. Grafana es una pila de visualización de código abierto que puede conectarse con todas las fuentes de datos. Puede utilizar Promethus y Grafana como proveedor de servicios de supervisión para supervisar visualmente el rendimiento de Milvus distributed. Para obtener más información, consulte <a href="/docs/es/monitor.md">Despliegue de servicios de supervisión</a>.</p></li>
<li><p>Milvus Backup Milvus Backup es una herramienta que permite a los usuarios realizar copias de seguridad y restaurar los datos de Milvus. Proporciona tanto CLI como API para adaptarse a diferentes escenarios de aplicación. Para más detalles, consulte <a href="/docs/es/milvus_backup_overview.md">Milvus Backup</a>.</p></li>
<li><p>Milvus Capture Data Change (CDC) Milvus-CDC puede capturar y sincronizar datos incrementales en las instancias de Milvus y garantiza la fiabilidad de los datos empresariales transfiriéndolos sin problemas entre las instancias de origen y de destino, lo que permite una copia de seguridad incremental y una recuperación de desastres sencillas. Para obtener más información, consulte <a href="/docs/es/milvus-cdc-overview.md">Milvus CDC</a>.</p></li>
<li><p>Conectores Milvus Milvus ha planificado un conjunto de conectores para que pueda integrar Milvus con herramientas de terceros, como Apache Spark. Actualmente, puede utilizar nuestro conector Spark para alimentar sus datos Milvus a Apache Spark para el procesamiento de aprendizaje automático. Para más detalles, consulte <a href="/docs/es/integrate_with_spark.md">Conector Spark-Milvus</a>.</p></li>
<li><p>Servicios de Transmisión Vectorial (VTS) Milvus proporciona un conjunto de herramientas para que usted pueda transferir sus datos entre una instancia Milvus y un montón de fuentes de datos, incluyendo clusters Zilliz, Elasticsearch, Postgres (PgVector), y otra instancia Milvus. Para más detalles, consulte <a href="https://github.com/zilliztech/vts">VTS</a>.</p></li>
</ul>
