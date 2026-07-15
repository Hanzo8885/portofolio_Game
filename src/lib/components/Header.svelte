<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import {
    isMobile,
    getCSSVariable,
    scrollTo,
    playSound,
  } from "$lib/logic/globalFunctions.js";

  let header;
  let logoHeader;
  let socials;

  let mobileButtons;

  let home;
  let portfolio;
  let about;

  let homeMobile;
  let portfolioMobile;
  let aboutMobile;

  let audiotoolSocialSVG;
  let youtubeSocialSVG;
  let instagramSocialSVG;
  let linkedinSocialSVG;
  let githubSocialSVG;

  let dropDownIsOpen = false;

  function updateThemeColors() {
    const headerButtons = [home, portfolio, about];
    const socialSVGs = [
      audiotoolSocialSVG,
      youtubeSocialSVG,
      instagramSocialSVG,
      linkedinSocialSVG,
      githubSocialSVG,
    ];

    headerButtons.forEach((button) => {
      const text = button.querySelector("span");
      gsap.killTweensOf(text);
      gsap.set(text, { color: getCSSVariable("--color-primary") });
    });

    socialSVGs.forEach((svg) => {
      const icon = svg.querySelector(".icon");
      gsap.killTweensOf(icon);
      gsap.set(icon, { fill: getCSSVariable("--color-primary") });
    });
  }

  onMount(() => {
    updateThemeColors();

    if (window.matchMedia) {
      window
        .matchMedia("(prefers-color-scheme: dark)")
        .addEventListener("change", () => {
          updateThemeColors();
        });
    }

    function openDropdown() {
      dropDownIsOpen = true;

      let timelineOpen = gsap.timeline();
      let timelineOpen2 = gsap.timeline();

      gsap.set(socials, { opacity: 0 });

      timelineOpen2
        .to(logoHeader, {
          y: "25",
          duration: 0.2,
          ease: "sine.inOut",
          onComplete: () => {
            mobileButtons.style.display = "grid";
          },
        })
        .to(logoHeader, {
          y: "0",
          duration: 0.5,
          ease: "sine.out",
        })
        .to(socials, {
          opacity: 1,
        });

      timelineOpen
        .to(header, {
          y: "20",
          duration: 0.1,
          ease: "sine.out",
          delay: 0.1,
        })
        .to(header, {
          marginTop: "0",
          borderRadius: 0,
          paddingLeft: 0,
          width: "100%",
          y: "0",
          height: "100%",
          duration: 0.5,
          ease: "circ.out",
          top: "auto",
        });
      let multiplier = 0;
      headerButtonsMobile.forEach((button) => {
        gsap.set(button, { opacity: 0, height: "0%" });
        multiplier++;
        gsap.to(button, {
          height: "100%",
          opacity: 1,
          duration: 0.5,
          delay: 0.1 * multiplier,
          onComplete: () => {
            socials.style.display = "flex";
          },
        });
      });
    }

    const animateCard = () => {
      const x = Math.random() * 500;
      const y = Math.random() * 500;
      const duration = 0.02;

      gsap.to(header, {
        backgroundPosition: `${x}px ${y}px`,
        duration,
        ease: "linear",
        onComplete: animateCard,
      });
    };

    animateCard();

    function closeDropdown() {
      dropDownIsOpen = false;

      let timelineClose = gsap.timeline();
      let timelineClose2 = gsap.timeline();

      let multiplier = 0;
      let buttonsAnimated = 0;

      headerButtonsMobile.forEach((button) => {
        multiplier++;
        gsap.to(socials, {
          opacity: 0,
          duration: 0.3,
        });
        gsap.to(button, {
          height: "0%",
          opacity: 0,
          duration: 0.2,
          delay: 0.1 * multiplier,
          onComplete: () => {
            buttonsAnimated++;

            if (buttonsAnimated === headerButtonsMobile.length) {
              mobileButtons.style.display = "none";
              socials.style.display = "none";

              timelineClose
                .to(header, {
                  width: "80px",
                  borderRadius: "50px",
                  height: "80px",
                  duration: 0.2,
                  ease: "circ.out",
                  top: "80%",
                })
                .to(header, {
                  paddingLeft: "10px",
                  y: "-20",
                  duration: 0.2,
                  ease: "circ.out",
                })
                .to(header, {
                  marginTop: "5%",
                  y: "0",
                  duration: 0.3,
                  ease: "sine.out",
                });

              timelineClose2
                .to(logoHeader, {
                  y: "-20",
                  duration: 0.3,
                  ease: "sine.out",
                  delay: 0.2,
                })
                .to(logoHeader, {
                  y: "0",
                  duration: 0.3,
                  ease: "sine.out",
                });
            }
          },
        });
      });
    }

    function toggleDropdown() {
      dropDownIsOpen ? closeDropdown() : openDropdown();
    }

    const headerButtons = [home, portfolio, about];
    const headerButtonsMobile = [homeMobile, portfolioMobile, aboutMobile];
    const socialSVGs = [
      audiotoolSocialSVG,
      youtubeSocialSVG,
      instagramSocialSVG,
      linkedinSocialSVG,
      githubSocialSVG,
    ];

    headerButtons.forEach((button) => {
      const overlay = button.querySelector(".headerButtonOverlay");
      const text = button.querySelector("span");

      let isHovering = false;

      gsap.set(overlay, { yPercent: 100 });

      button.addEventListener("mouseenter", () => {
        isHovering = true;
        playSound("hover");
        gsap.fromTo(
          overlay,
          { yPercent: 100 },
          {
            yPercent: 0,
            duration: 0.4,
            ease: "power2.out",
            overwrite: "auto",
          },
        );

        gsap.to(text, {
          color: getCSSVariable("--color-basic"),
          duration: 0.3,
          ease: "power2.out",
          overwrite: "auto",
        });

        gsap.to(button, {
          scale: 0.9,
          duration: 0.2,
        });
      });

      button.addEventListener("mousedown", () => {
        playSound("openLink");
        isHovering = false;
        gsap.to(button, {
          scale: 0.8,
          duration: 0.1,
        });
      });

      button.addEventListener("click", () => {
        let tl = gsap.timeline();

        tl.to(button, {
          scale: 0.7,
          duration: 0.1,
          ease: "circ.out",
        });
        tl.to(button, {
          scale: 1.1,
          duration: 0.2,
          ease: "circ.Out",
        });
        if (!isHovering) {
          tl.to(button, {
            scale: 1,
            duration: 0.1,
          });
        } else if (isHovering) {
          tl.to(button, {
            scale: 0.8,
            boxShadow: "0 0 0 0px",
            duration: 0.1,
          });
        }
      });

      button.addEventListener("mouseleave", () => {
        gsap.to(button, {
          scale: 1,
          duration: 0.2,
        });

        gsap.fromTo(
          overlay,
          { yPercent: 0 },
          {
            yPercent: -100,
            duration: 0.4,
            ease: "power2.in",
            overwrite: "auto",
          },
        );

        gsap.to(text, {
          color: getCSSVariable("--color-primary"),
          duration: 0.6,
          ease: "power2.out",
          overwrite: "auto",
        });
      });
    });

    headerButtonsMobile.forEach((button) => {
      const overlay = button.querySelector(".headerButtonOverlay");
      const text = button.querySelector("span");

      let isHovering = false;

      gsap.set(overlay, { yPercent: 100, opacity: 0 });

      button.addEventListener("click", () => {
        if (dropDownIsOpen) {
          closeDropdown();
        }
      });

      button.addEventListener("mouseenter", () => {
        playSound("hover");
        isHovering = true;
        gsap.fromTo(
          overlay,
          { yPercent: 100 },
          {
            yPercent: 0,
            borderRadius: "10px",
            duration: 0.4,
            opacity: 1,
            ease: "power2.out",
            overwrite: "auto",
          },
        );

        gsap.to(text, {
          color: getCSSVariable("--color-basic"),
          duration: 0.3,
          ease: "power2.out",
          overwrite: "auto",
        });

        gsap.to(button, {
          scale: 0.9,
          duration: 0.2,
        });
      });

      button.addEventListener("mousedown", () => {
        playSound("openLink");
        isHovering = false;
        gsap.to(button, {
          scale: 0.8,
          duration: 0.1,
        });
      });

      button.addEventListener("click", () => {
        let tl = gsap.timeline();

        tl.to(button, {
          scale: 0.7,
          duration: 0.1,
          ease: "circ.out",
        });
        tl.to(button, {
          scale: 1.1,
          duration: 0.2,
          ease: "circ.Out",
        });
        if (!isHovering) {
          tl.to(button, {
            scale: 0.9,
            duration: 0.3,
          });
        } else if (isHovering) {
          tl.to(button, {
            scale: 0.8,
            boxShadow: "0 0 0 0px",
            duration: 0.1,
          });
        }
      });

      button.addEventListener("mouseleave", () => {
        gsap.to(button, {
          scale: 1,
          duration: 0.2,
        });

        gsap.fromTo(
          overlay,
          { yPercent: 0 },
          {
            yPercent: -100,
            duration: 0.4,
            opacity: 0,
            ease: "power2.in",
            overwrite: "auto",
          },
        );

        gsap.to(text, {
          color: getCSSVariable("--color-primary"),
          duration: 0.6,
          ease: "power2.out",
          overwrite: "auto",
        });
      });
    });

    function handleResize(e) {
      if (!logoHeader) return;

      const logoBox = logoHeader.getBoundingClientRect();

      if (e.matches) {
        gsap.set(header, {
          marginTop: "5%",
          padding: "10px",
          width: logoBox.width,
          borderRadius: "50px",
          top: "80%",
        });
        mobileButtons.style.display = dropDownIsOpen ? "grid" : "none";
        socials.style.display = dropDownIsOpen ? "flex" : "none";
      } else {
        dropDownIsOpen = false;

        gsap.set(header, {
          marginTop: "10px",
          borderRadius: "20px",
          width: "74%",
          height: "auto",
          top: "auto",
          y: 0,
        });

        mobileButtons.style.display = "none";
        socials.style.display = "flex";
        gsap.set(socials, { opacity: 1 });
      }
    }

    const mediaQuery = window.matchMedia("(max-width: 1023px)");
    mediaQuery.addEventListener("change", handleResize);
    handleResize(mediaQuery);

    socialSVGs.forEach((svg) => {
      const parent = svg.parentElement;

      parent.addEventListener("mouseenter", () => {
        playSound("hover");
        gsap.to(svg.querySelector(".icon"), {
          fill: getCSSVariable("--color-primary"),
          duration: 0.2,
        });
        gsap.to(parent, {
          scale: 1.3,
          duration: 0.2,
        });
      });

      parent.addEventListener("mousedown", () => {
        playSound("openLink");
      });

      parent.addEventListener("mouseleave", () => {
        gsap.to(svg.querySelector(".icon"), {
          duration: 0.2,
        });
        gsap.to(parent, {
          scale: 1,
          duration: 0.2,
        });
      });
    });

    logoHeader.addEventListener("click", () => {
      playSound("openLink");
      gsap.fromTo(
        logoHeader,
        { scale: 1 },
        {
          scale: 0.7,
          duration: 0.1,
          ease: "sine.out",
          repeat: 1,
          yoyo: true,
        },
      );

      if (isMobile()) {
        toggleDropdown();
        return;
      }
    });

    let hoverAnim;
    logoHeader.addEventListener("mouseenter", () => {
      playSound("hover");
      hoverAnim = gsap.to(logoHeader.querySelector(".logo"), {
        x: gsap.utils.random(50, -50),
        y: gsap.utils.random(50, -50),
        yoyo: true,
        repeat: -1,
        duration: 0.3,
        ease: "sine.inOut",
      });
    });

    logoHeader.addEventListener("mouseleave", () => {
      hoverAnim.kill();
      hoverAnim = null;

      gsap.to(logoHeader.querySelector(".logo"), {
        x: 0,
        y: 0,
        duration: 0.5,
        ease: "circ.out",
      });
    });
  });
</script>

<header bind:this={header} id="header" class="card noBounce">
  <div id="left">
    <svg
      bind:this={logoHeader}
      id="logoHeader"
      version="1.2"
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 1000 1000"
    >
      <path
        id="Color Fill 1"
        fill-rule="evenodd"
        class="logo interactable"
        d="m553.9 225.8c27.9 3.4 72.2 35.2 95 48.7 84 50.1 173.4 96.9 236.2 168.2-2.6 46.3-58.8 78.4-93.7 93.7-15.5 6.9-38 14.2-47.5 27-9.6 13-13.7 29.4-26.9 38.5q-11.6 5.8-23.1 11.6c-16.8 20.5-35.7 45.4-57.8 60.3-14.8 10-28.6 24.5-43.7 32.1q-9 2.6-17.9 5.1c-19.9 17.7-36.2 59.2-68.1 62.9-40.5 4.8-58.9-43.3-80.9-62.9q-8.9-2.5-17.9-5.1c-15.1-7.6-28.9-22.1-43.7-32.1-22-14.9-41-39.8-57.8-60.3q-11.5-5.8-23.1-11.6c-13.2-9.1-17.3-25.5-26.9-38.5-9.5-12.8-32-20.1-47.5-27-35-15.4-88.7-46.8-93.7-91.1 73.6-87.1 207-159.8 314.5-213.1q9 25 18 50.1c8.3 12.3 22.7 18.4 28.2 34.6 6 17.9 9.9 48.3 14.1 64.2q2 24.4 3.9 48.8 0.6-1.3 1.3-2.6c7.6-10.9 18.8-35.9 14.1-56.5-1.4-6.3-8.4-14.3-5.1-25.6q15.4-23.8 30.8-47.5c-7.4-43.8 5.7-40.6 19.2-71.9z"
      />
    </svg>
    <nav class="buttons">
      <a
        bind:this={home}
        aria-label="Home"
        class="headerButton interactable"
        href="/"
        ><span>Home</span>
        <div class="headerButtonOverlay"></div>
      </a>
      <a
        bind:this={portfolio}
        aria-label="portfolio"
        href="/"
        class="headerButton interactable"
        on:click={() => {
          scrollTo("#projectsSection");
        }}
        ><span>Portfolio</span>
        <div class="headerButtonOverlay"></div>
      </a>
      <a
        bind:this={about}
        aria-label="archive"
        href="/archive"
        class="headerButton interactable"
        ><span>Archive</span>
        <div class="headerButtonOverlay"></div>
      </a>
    </nav>
  </div>
  <div class="buttons_mobile" bind:this={mobileButtons}>
    <a
      bind:this={homeMobile}
      aria-label="Home"
      class="headerButton interactable"
      href="/"
      ><span>Home</span>
      <div class="headerButtonOverlay"></div>
    </a>
    <a
      bind:this={portfolioMobile}
      aria-label="portfolio"
      href="/"
      class="headerButton interactable"
      on:click={() => {
        scrollTo("#projectsSection");
      }}
      ><span>Portfolio</span>
      <div class="headerButtonOverlay"></div>
    </a>
    <a
      bind:this={aboutMobile}
      aria-label="archive"
      href="/archive"
      class="headerButton interactable"
      ><span>Archive</span>
      <div class="headerButtonOverlay"></div>
    </a>
  </div>
  <div class="socials" bind:this={socials}>

    <a
      aria-label="YouTube"
      href="https://www.youtube.com/@hanzogokil1245"
      target="_blank"
      rel="noopener noreferrer"
    >
      <svg
        bind:this={youtubeSocialSVG}
        class="socialSVG interactable"
        id="youtubeLogo"
        version="1.2"
        xmlns="https://www.w3.org/2000/svg"
        viewBox="-0.5 -0.6 25 25"
      >
        <path
          class="icon"
          d="M23.498 6.186a3 3 0 0 0-2.113-2.121C19.43 3.5 12 3.5 12 3.5s-7.43 0-9.385.565A3 3 0 0 0 .502 6.186C0 8.165 0 12 0 12s0 3.835.502 5.814a3 3 0 0 0 2.113 2.121C4.57 20.5 12 20.5 12 20.5s7.43 0 9.385-.565a3 3 0 0 0 2.113-2.121C24 15.835 24 12 24 12s0-3.835-.502-5.814zM9.75 15.5v-7l6 3.5-6 3.5z"
        />
      </svg>
    </a>

    <a
      aria-label="Instagram"
      href="https://www.instagram.com/kinemon973/"
      target="_blank"
      rel="noopener noreferrer"
    >
      <svg
        bind:this={instagramSocialSVG}
        class="socialSVG interactable"
        id="instagramLogo"
        version="1.2"
        xmlns="https://www.w3.org/2000/svg"
        viewbox="-2 -2 28 28"
      >
        <path
          class="icon"
          d="M11.9962 0.0078125C8.73824 0.0078125 8.32971 0.021622 7.05019 0.080003C5.77333 0.138241 4.90129 0.341051 4.13824 0.637622C3.34938 0.944146 2.68038 1.35434 2.01343 2.02124C1.34652 2.68819 0.936333 3.35719 0.629809 4.14605C0.333238 4.9091 0.130429 5.78115 0.0721905 7.058C0.0138095 8.33753 0 8.74605 0 12.0041C0 15.262 0.0138095 15.6705 0.0721905 16.9501C0.130429 18.2269 0.333238 19.099 0.629809 19.862C0.936333 20.6509 1.34652 21.3199 2.01343 21.9868C2.68038 22.6537 3.34938 23.0639 4.13824 23.3705C4.90129 23.667 5.77333 23.8698 7.05019 23.9281C8.32971 23.9864 8.73824 24.0002 11.9962 24.0002C15.2542 24.0002 15.6627 23.9864 16.9422 23.9281C18.2191 23.8698 19.0911 23.667 19.8542 23.3705C20.643 23.0639 21.312 22.6537 21.979 21.9868C22.6459 21.3199 23.0561 20.6509 23.3627 19.862C23.6592 19.099 23.862 18.2269 23.9202 16.9501C23.9786 15.6705 23.9924 15.262 23.9924 12.0041C23.9924 8.74605 23.9786 8.33753 23.9202 7.058C23.862 5.78115 23.6592 4.9091 23.3627 4.14605C23.0561 3.35719 22.6459 2.68819 21.979 2.02124C21.312 1.35434 20.643 0.944146 19.8542 0.637622C19.0911 0.341051 18.2191 0.138241 16.9422 0.080003C15.6627 0.021622 15.2542 0.0078125 11.9962 0.0078125ZM7.99748 12.0041C7.99748 14.2125 9.78776 16.0028 11.9962 16.0028C14.2047 16.0028 15.995 14.2125 15.995 12.0041C15.995 9.79557 14.2047 8.00529 11.9962 8.00529C9.78776 8.00529 7.99748 9.79557 7.99748 12.0041ZM5.836 12.0041C5.836 8.60181 8.594 5.84381 11.9962 5.84381C15.3984 5.84381 18.1564 8.60181 18.1564 12.0041C18.1564 15.4062 15.3984 18.1642 11.9962 18.1642C8.594 18.1642 5.836 15.4062 5.836 12.0041ZM18.3998 7.03996C19.1949 7.03996 19.8394 6.39548 19.8394 5.60043C19.8394 4.80538 19.1949 4.16086 18.3998 4.16086C17.6048 4.16086 16.9603 4.80538 16.9603 5.60043C16.9603 6.39548 17.6048 7.03996 18.3998 7.03996Z"
        />
      </svg>
    </a>
    <a
      aria-label="GitHub"
      href="https://github.com/Hanzo8885"
      target="_blank"
      rel="noopener noreferrer"
    >
      <svg
        bind:this={githubSocialSVG}
        class="socialSVG interactable"
        id="githubLogo"
        version="1.2"
        xmlns="https://www.w3.org/2000/svg"
        viewBox="0 0 100 100"
      >
        <path
          class="icon"
          d="M48.854 0C21.839 0 0 22 0 49.217c0 21.756 13.993 40.172 33.405 46.69 2.427.49 3.316-1.059 3.316-2.362 0-1.141-.08-5.052-.08-9.127-13.59 2.934-16.42-5.867-16.42-5.867-2.184-5.704-5.42-7.17-5.42-7.17-4.448-3.015.324-3.015.324-3.015 4.934.326 7.523 5.052 7.523 5.052 4.367 7.496 11.404 5.378 14.235 4.074.404-3.178 1.699-5.378 3.074-6.6-10.839-1.141-22.243-5.378-22.243-24.283 0-5.378 1.94-9.778 5.014-13.2-.485-1.222-2.184-6.275.486-13.038 0 0 4.125-1.304 13.426 5.052a46.97 46.97 0 0 1 12.214-1.63c4.125 0 8.33.571 12.213 1.63 9.302-6.356 13.427-5.052 13.427-5.052 2.67 6.763.97 11.816.485 13.038 3.155 3.422 5.015 7.822 5.015 13.2 0 18.905-11.404 23.06-22.324 24.283 1.78 1.548 3.316 4.481 3.316 9.126 0 6.6-.08 11.897-.08 13.526 0 1.304.89 2.853 3.316 2.364 19.412-6.52 33.405-24.935 33.405-46.691C97.707 22 75.788 0 48.854 0z"
        />
      </svg>
    </a>
  </div>
</header>

<style>
  .logo {
    fill: var(--color-primary);
    cursor: pointer;
  }

  .card {
    aspect-ratio: unset;
  }

  #logoHeader {
    width: 80px;
  }
</style>
