<script>
  import { onMount } from "svelte";
  import { client } from "$lib/logic/data.js";
  import { renderBody, formatDateTime } from "$lib/logic/formatter";
  import { getImage } from "$lib/logic/data.js";
  import Card from "$lib/components/Card.svelte";
  import Button from "$lib/components/Button.svelte";
  import PageHeader from "$lib/components/PageHeader.svelte";
  import { isDarkMode } from "$lib/logic/globalFunctions";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import { goto } from "$app/navigation";

  // --- Impor Foto untuk About Section ---
  import fotoAbout from "$lib/videos/fotosaya.jpeg";

  let posts = [];
  let video; // Untuk video header saja
  let videoSource;

  import headerVideoDay from "$lib/videos/day.mp4";
  import headerVideoNight from "$lib/videos/night.mp4";

  function setVideo() {
    videoSource = isDarkMode() ? headerVideoNight : headerVideoDay;
    if (video) video.load();
  }

  function animateIn(e) {
    const info = e.currentTarget.querySelector(".info");
    const img = info.parentElement.querySelector("#mainImage");
    const button = info.querySelector("input");
    gsap.to(info, { opacity: 1, scale: 1, duration: 0.3, ease: "power2.out", onComplete: () => { if (button) button.disabled = false; } });
    gsap.to(img, { filter: "blur(10px)", ease: "power2.out", duration: 0.3 });
  }

  function animateOut(e) {
    const info = e.currentTarget.querySelector(".info");
    const button = info.querySelector("input");
    const img = info.parentElement.querySelector("#mainImage");
    gsap.to(info, { opacity: 0, scale: 0.9, duration: 0.3, ease: "power2.out", onComplete: () => { if (button) button.disabled = true; } });
    gsap.to(img, { filter: "blur(0px)", ease: "power2.out", duration: 0.3 });
    if (button) button.disabled = true;
  }

  onMount(async () => {
    gsap.registerPlugin(ScrollTrigger);
    setVideo();

    // Scroll scrub hanya untuk video di header
    ScrollTrigger.create({
      trigger: video,
      start: "top top",
      end: "+=650",
      scrub: 1,
      onUpdate: (self) => {
        if (video && isFinite(video.duration)) video.currentTime = video.duration * self.progress;
      },
    });

    posts = await client.fetch('*[_type == "post"]{title,slug,mainImage{asset->{_id,url},alt},categories[]->{title},homepage,featured,subcategories[]->{title},created,body,links}');
    posts.sort((a, b) => (b.featured === true) - (a.featured === true) || new Date(b.created) - new Date(a.created));
  });
</script>

<title>Alfikar:portfolio</title>
<div id="content">
  <PageHeader id="welcomeHeader">
    <div class="video-container">
      <video bind:this={video} muted playsinline preload="auto" id="homeVideo">
        <source src={videoSource} type="video/mp4" />
      </video>
    </div>
    <div class="headerContent">
      <h1 class="emphasis">Hiya, i'm <span class="name">Alfikar</span>!</h1>
      <h2 class="emphasis">A Game Developer, Musician, Content Creator<br /><span>(& a lil' bit of everything else :])</span></h2>
    </div>
  </PageHeader>

  <section id="aboutSection">
    <h1 class="sectionTitle">- Who am I? -</h1>
    <Card hasSlug={false} className="aboutCard">
      <div id="aboutLayout">
        <!-- Foto Statis -->
        <img src={fotoAbout} alt="Alfikar" id="aboutVideo" />
        
        <p id="aboutText">
          I'm <span class="highlightedText">Alfikar</span>, known as <span class="highlightedText">althruist</span>
          online. I am an 18 y/o in Malta, reading for
          <span class="highlightedText">Bachelors of Science (Hons) in Digital Games Development.</span>
          <br />I like to do a variety of things; <br /><br /> Ranging from
          <span class="highlightedText">3D Art/Animation </span> (using Blender), 
          <span class="highlightedText"> Music-Making, Coding </span> in several languages,
          <span class="highlightedText"> Concept Art, Sound Design, Photography</span>
          .. anything creative you can think of I probably do it!
        </p>
      </div>
    </Card>
  </section>

  <!-- Section Projects tetap sama -->
</div>

<style>
  #aboutVideo {
    width: 250px;
    height: 250px;
    margin: 20px;
    border-radius: 20px;
    object-fit: cover;
  }
  /* Sisipkan sisa style asli Anda di sini */
  #aboutLayout { flex-direction: column; justify-content: center; align-items: center; display: flex; }
  .sectionTitle { font-family: "Althite"; font-size: 2rem; margin: auto; padding: 20px; text-align: center; white-space: nowrap; }
  @media (min-width: 1024px) { #aboutLayout { flex-direction: row; } }
</style>