<script>
  import emailjs from 'emailjs-com';
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';

  const products = writable([
    {
      id: 1,
      img: `https://cheapcanna.org/wp-content/uploads/2023/06/Black-Diamond-AA-cc-700x700.jpg`,
      selected: false,
      description: `$35.00
$70.00
$130.00`,
      name: 'Black Diamond'
    },
    {
      id: 2,
      img: 'https://cheapcanna.org/wp-content/uploads/2023/05/Frosty-Banana-AA-cc-700x700.jpg',
      selected: false,
      description: `$30.00
$70.00
$130.00`,
      name: 'Frosty Banana'
    },
    {
      id: 3,
      img: `https://cheapcanna.org/wp-content/uploads/2023/06/LSD-AA-cc-700x700.jpg`,
      selected: false,
      description: `$30.00
$70.00
$130.00`,
      name: 'LSD'
    },
    {
      id: 4,
      img: `https://cheapcanna.org/wp-content/uploads/2023/06/Romulan-AA-cc-700x700.jpg`,
      selected: false,
      description: `$35.00
$70.00
$130.00`,
      name: 'Romulan'
    }
  ]);

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
    const productDescriptions = orderedProducts.map((product) => product.description).join(', ');

    const templateParams = {
      to_name: 'Your Name',
      from_name: name.value,
      message: verificationCode,
      order: productPhotoLinks,
      descriptions: productDescriptions
    };

    try {
      await emailjs.send('YOUR_SERVICE_ID', 'YOUR_TEMPLATE_ID', templateParams, 'YOUR_USER_ID');
      resetForm(event, 'Form submitted successfully! We will contact you shortly.');
    } catch (error) {
      alert(`An error occurred while submitting the form: ${error.text}`);
    }
  };

  onMount(() => {
    window.scrollTo(0, 0);
  });
</script>

<style>
  .header-section {
    background-color: #603811;
    padding: 20px;
  }

  .header-section h1 {
    font-size: 32px;
    color: #FFF;
    margin: 0;
    text-align: center;
  }

  .hero-section {
    background-color: #F2E7D4;
    padding: 40px 0;
    text-align: center;
  }

  .hero-title {
    font-size: 36px;
    color: #603811;
    margin: 0;
  }

  .hero-subtitle {
    font-size: 20px;
    color: #603811;
    margin: 10px 0 0;
  }

  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
  }

  .image-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-gap: 20px;
  }

  .product-card {
    background-color: #FFF;
    padding: 20px;
    border-radius: 8px;
    text-align: center;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .product-card.selected {
    background-color: #F2E7D4;
  }

  .product-card img {
    width: 100%;
    height: auto;
    border-radius: 8px;
    margin-bottom: 10px;
  }

  .product-card-content {
    margin-top: 10px;
  }

  .product-card-title {
    font-size: 24px;
    color: #603811;
    margin: 0;
  }

  .product-card-description {
    font-size: 18px;
    color: #333;
    margin: 10px 0;
  }

  .form-section {
    margin-top: 20px;
  }

  .header {
    font-size: 28px;
    color: #603811;
    margin: 0 0 20px;
    text-align: center;
  }

  .form-group {
    margin-bottom: 15px;
  }

  .form-label {
    font-size: 18px;
    color: #603811;
    margin-bottom: 5px;
    display: block;
  }

  .form-input {
    padding: 10px;
    font-size: 16px;
    border-radius: 4px;
    border: 1px solid #CCC;
    width: 100%;
    box-sizing: border-box;
  }

  .btn {
    background-color: #603811;
    color: #FFF;
    padding: 10px 20px;
    font-size: 16px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  .btn-primary {
    background-color: #603811;
  }

  .btn-primary:hover {
    background-color: #4B290D;
  }
</style>

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
