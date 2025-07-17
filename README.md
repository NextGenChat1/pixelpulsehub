# pixelpulsehub
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1"/>
  <title>PixelPulseHub – Lifestyle & News</title>
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>💜</text></svg>">
  <style>
    :root{
      --bg:#fff; --bgSoft:#f7f7f7; --text:#111;
      --accent:#8a2be2; --accent2:#ff1493;
      --radius:12px; --shadow:0 2px 6px rgba(0,0,0,.08);
      --shadowHover:0 4px 14px rgba(0,0,0,.15);
    }
    [data-theme="dark"]{--bg:#121212;--bgSoft:#1e1e1e;--text:#e5e5e5;}
    *{box-sizing:border-box;margin:0;padding:0}
    body{font-family:system-ui,sans-serif;background:var(--bg);color:var(--text);line-height:1.6}
    header{background:linear-gradient(90deg,var(--accent),var(--accent2));color:#fff;padding:1rem;display:flex;justify-content:space-between;align-items:center}
    header h1{font-size:1.5rem}
    #themeToggle{background:transparent;border:none;font-size:1.3rem;cursor:pointer;color:#fff}
    nav{position:sticky;top:0;background:var(--bgSoft);display:flex;gap:.5rem;overflow-x:auto;padding:.5rem 1rem;z-index:10}
    nav a{flex:0 0 auto;padding:.4rem .8rem;border-radius:var(--radius);text-decoration:none;color:var(--text);white-space:nowrap}
    nav a:hover{background:var(--accent);color:#fff}
    main{max-width:900px;margin:1.5rem auto;padding:0 1rem}
    section{margin-bottom:2rem}
    .card{background:var(--bgSoft);border-radius:var(--radius);padding:1rem;margin-bottom:1rem;box-shadow:var(--shadow);transition:.2s}
    .card:hover{box-shadow:var(--shadowHover)}
    .card h3{margin-bottom:.5rem;color:var(--accent)}
    .card img{width:100%;border-radius:var(--radius);margin-top:.5rem}
    #addpost form{display:flex;flex-direction:column;gap:.5rem}
    #addpost textarea{resize:vertical;min-height:120px;padding:.5rem;border-radius:var(--radius);border:1px solid #ccc;background:var(--bg);color:var(--text)}
    button[type="submit"]{align-self:flex-start;padding:.5rem 1rem;border:none;border-radius:var(--radius);background:var(--accent);color:#fff;cursor:pointer}
    footer{text-align:center;padding:2rem 1rem;font-size:.9rem;color:#888}
  </style>
</head>
<body>
  <!-- HEADER -->
  <header>
    <h1>PixelPulseHub</h1>
    <button id="themeToggle" aria-label="Toggle dark mode">🌗</button>
  </header>

  <!-- STICKY NAV -->
  <nav>
    <a href="#breaking">Breaking</a>
    <a href="#horoscope">Horoscope</a>
    <a href="#tips">Tips</a>
    <a href="#recipes">Recipes</a>
    <a href="#poetry">Poetry</a>
    <a href="#wishes">Wishes</a>
    <a href="#stories">Stories</a>
    <a href="#books">Books</a>
    <a href="#wallpapers">Wallpapers</a>
    <a href="#addpost">+ Post</a>
  </nav>

  <main id="content"></main>

  <footer>&copy; 2025 PixelPulseHub. Made with 💜.</footer>

  <script>
    /* DARK MODE */
    const toggleBtn=document.getElementById('themeToggle');
    toggleBtn.onclick=()=>{
      const dark=document.documentElement.getAttribute('data-theme')==='dark';
      document.documentElement.setAttribute('data-theme',dark?'light':'dark');
      localStorage.setItem('theme',dark?'light':'dark');
    };
    document.documentElement.setAttribute('data-theme',localStorage.getItem('theme')||'light');

    /* SECTIONS */
    const sections=[
      {id:'breaking',title:'Breaking News'},
      {id:'horoscope',title:'Horoscope'},
      {id:'tips',title:'Tips & Hacks'},
      {id:'recipes',title:'Recipes'},
      {id:'poetry',title:'Poetry'},
      {id:'wishes',title:'Wishes & Quotes'},
      {id:'stories',title:'Short Stories'},
      {id:'books',title:'Book Recommendations'},
      {id:'wallpapers',title:'Wallpapers'}
    ];

    function createCard(t,b,i=''){
      const d=document.createElement('div');d.className='card';
      d.innerHTML=`<h3>${t}</h3><p>${b}</p>${i?`<img src="${i}" alt="">`:''}`;
      return d;
    }

    function loadSection(id){
      const el=document.getElementById(id);
      if(!el)return;
      el.innerHTML='';
      el.appendChild(createCard(`${id} update`,`Fresh ${id} content generated at ${new Date().toLocaleTimeString()}.\n(Copy-paste AI text here to replace)`));
    }

    function renderPage(){
      const main=document.getElementById('content');
      sections.forEach(({id,title})=>{
        const s=document.createElement('section');s.id=id;
        s.innerHTML=`<h2>${title}</h2>`;
        main.appendChild(s);
        loadSection(id);
      });

      /* add post form */
      const a=document.createElement('section');a.id='addpost';
      a.innerHTML=`
        <h2>Add Your Post</h2>
        <form id="postForm">
          <textarea placeholder="Write anything..."></textarea>
          <button type="submit">Post</button>
        </form>
      `;
      main.appendChild(a);

      /* user posts */
      const renderU=()=>{
        const arr=JSON.parse(localStorage.getItem('userPosts')||'[]');
        let s=document.getElementById('userPosts');
        if(s)s.remove();
        s=document.createElement('section');s.id='userPosts';
        s.innerHTML='<h2>User Posts</h2>';
        arr.forEach(p=>s.appendChild(createCard('User',p)));
        main.appendChild(s);
      };
      renderU();
      document.getElementById('postForm').onsubmit=e=>{
        e.preventDefault();
        const txt=e.target.querySelector('textarea').value.trim();
        if(!txt)return;
        const arr=JSON.parse(localStorage.getItem('userPosts')||'[]');
        arr.unshift(txt);
        localStorage.setItem('userPosts',JSON.stringify(arr));
        e.target.reset();
        renderU();
      };
    }
    renderPage();

    /* AUTO REFRESH */
    setInterval(()=>loadSection('breaking'),3*60*60*1000); // every 3h
    const daily=()=>sections.forEach(s=>s.id!=='breaking'&&loadSection(s.id));
    daily();
    setInterval(daily,24*60*60*1000);
  </script>
</body>
</html>
