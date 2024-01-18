<script>
    import "../app.css";
    import Footer from "../components/Footer.svelte";
    import Header from "../components/Header.svelte";
  </script>
  <div class="relative max-w-[1440px] mx-auto w-full text-sm sm:text-base min-h-screen">
    <Header/>
        <slot />
    <Footer/>
  </div>