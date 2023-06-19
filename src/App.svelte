<script>
  import emailjs from 'emailjs-com';
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';

  const categories = ['cannabis', 'marijuana', 'weed', 'ganja'];

  const products = writable(categories.map((category, id) => {
    return {
      id: id + 1,
      img: `https://source.unsplash.com/random/300x200/?${category}`,
      selected: false,
      description: `This is a high-quality product in the category of ${category}.`,
      name: 'Product ' + (id + 1)
    };
  }));

  const toggleSelection = (product) => {
    products.update(allProducts => {
      return allProducts.map(p => p.id === product.id ? { ...p, selected: !p.selected } : p);
    });
  };

  const resetForm = (event, alertMessage) => {
    event.target.reset();
    products.update(allProducts => {
      return allProducts.map(p => ({ ...p, selected: false }));
    });
    alert(alertMessage);
  };

  const generateVerificationCode = () => {
    return Math.floor(100000 + Math.random() * 900000);
  };

  const handleSubmit = async (event) => {
    event.preventDefault();

    const { name, email, phone } = event.target.elements;

    let orderedProducts;
    products.subscribe(allProducts => {
      orderedProducts = allProducts.filter((p) => p.selected);
    })();

    if (!orderedProducts.length) {
      alert('Please select at least one product for ordering.');
      return;
    }

    const verificationCode = generateVerificationCode();
    const productPhotoLinks = orderedProducts.map((product) => product.img).join(', ');

    const templateParams = {
      to_name: 'Your Name',
      from_name: name.value,
      message: verificationCode,
      order: productPhotoLinks
    };

    try {
      await emailjs.send('drw4953', 'template_2ik2luv', templateParams, 'uKJs24_n7AgzyaHKF');
      resetForm(event, 'Form submitted successfully! We will contact you shortly.');
    } catch (error) {
      alert(`An error occurred while submitting the form: ${error.text}`);
    }
  };

  onMount(() => {
    window.scrollTo(0, 0);
  });
</script>

<div class="header-section">
  <h1>Welcome to Amora</h1>
</div>

<div class="hero-section">
  <h2 class="hero-title">Discover the Finest Cannabis Products</h2>
  <p class="hero-subtitle">Browse our selection and place your order</p>
</div>

<div class="container">
  <div class="image-grid">
    {#each $products as product}
      <div class="product-card {product.selected ? 'selected' : ''}" on:click={() => toggleSelection(product)}>
        <img src={product.img} alt={product.name} />
        <div class="product-card-content">
          <h2 class="product-card-title">{product.name}</h2>
          <p class="product-card-description">{product.description}</p>
        </div>
      </div>
    {/each}
  </div>

  <div class="form-section">
    <h2 class="header">Submit Details</h2>
    <form class="form" on:submit={handleSubmit}>
      <div class="form-group">
        <label class="form-label" for="name">Full Name:</label>
        <input class="form-input" id="name" name="name" type="text" required>
      </div>
      <div class="form-group">
        <label class="form-label" for="email">Email:</label>
        <input class="form-input" id="email" name="email" type="email" required>
      </div>
      <div class="form-group">
        <label class="form-label" for="phone">Phone:</label>
        <input class="form-input" id="phone" name="phone" type="text" required>
      </div>
      <button class="btn btn-primary" type="submit">Submit</button>
    </form>
  </div>
</div>