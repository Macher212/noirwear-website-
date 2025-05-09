// Admin-fähige NOIRWEAR-Website mit lokal bearbeitbaren Produkten und Passwortschutz für Admin-Modus
import React, { useState, useEffect } from "react";

export default function NOIRWEAR() {
  const [cartItems, setCartItems] = useState([]);
  const [showForm, setShowForm] = useState(false);
  const [selectedProduct, setSelectedProduct] = useState(null);
  const [formData, setFormData] = useState({ name: "", email: "", message: "" });
  const [successMessage, setSuccessMessage] = useState("");
  const [errorMessage, setErrorMessage] = useState("");
  const [searchQuery, setSearchQuery] = useState("");
  const [isAdmin, setIsAdmin] = useState(false);
  const [isVerifying, setIsVerifying] = useState(false);
  const [passwordInput, setPasswordInput] = useState("");
  const [products, setProducts] = useState(() => {
    const saved = localStorage.getItem("products");
    return saved
      ? JSON.parse(saved)
      : [
          { id: 1, name: "T-Shirt", description: "Schwarzes T-Shirt mit Neon-Print", price: 25 },
          { id: 2, name: "Hoodie", description: "Warmer Hoodie mit Kapuze", price: 50 },
          { id: 3, name: "Cap", description: "Stylische Cap in Neonfarben", price: 15 },
          { id: 4, name: "Jogginghose", description: "Bequeme schwarze Jogginghose", price: 35 },
          { id: 5, name: "Jacke", description: "Winddichte schwarze Jacke mit Neon-Akzent", price: 70 },
          { id: 6, name: "Sneaker", description: "Sportliche Schuhe mit gelben Details", price: 60 },
        ];
  });

  useEffect(() => {
    localStorage.setItem("products", JSON.stringify(products));
  }, [products]);

  const handleAddToCart = (item) => setCartItems([...cartItems, item]);
  const handleOpenOrderForm = (product) => {
    setSelectedProduct(product);
    setShowForm(true);
    setSuccessMessage("");
    setErrorMessage("");
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!formData.name || !formData.email) {
      setErrorMessage("Bitte fülle alle erforderlichen Felder aus.");
      return;
    }
    setSuccessMessage(`Danke, ${formData.name}! Deine Bestellung für '${selectedProduct.name}' wurde empfangen.`);
    setFormData({ name: "", email: "", message: "" });
    setShowForm(false);
  };

  const handleProductChange = (index, field, value) => {
    const updated = [...products];
    updated[index][field] = field === "price" ? parseFloat(value) : value;
    setProducts(updated);
  };

  const handleAddProduct = () => {
    const newProduct = { id: Date.now(), name: "Neues Produkt", description: "Beschreibung", price: 0 };
    setProducts([...products, newProduct]);
  };

  const handleDeleteProduct = (index) => {
    const updated = [...products];
    updated.splice(index, 1);
    setProducts(updated);
  };

  const verifyPassword = () => {
    if (passwordInput === "admin123") {
      setIsAdmin(true);
      setIsVerifying(false);
      setPasswordInput("");
    } else {
      alert("Falsches Passwort!");
    }
  };

  const filteredProducts = products.filter((p) =>
    p.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
    p.description.toLowerCase().includes(searchQuery.toLowerCase())
  );

  return (
    <div style={{ minHeight: "100vh", background: "black", color: "white", padding: "20px", fontFamily: "sans-serif" }}>
      <h1 style={{ fontSize: "72px", fontWeight: "bold", color: "#fffc00", textShadow: "0 0 25px #fffc00" }}>NOIRWEAR</h1>

      {isAdmin ? (
        <button
          onClick={() => setIsAdmin(false)}
          style={{ marginBottom: 20, background: "#fffc00", color: "black", padding: "8px", border: "none" }}
        >
          Admin-Modus deaktivieren
        </button>
      ) : isVerifying ? (
        <div style={{ marginBottom: 20 }}>
          <input
            type="password"
            placeholder="Admin-Passwort"
            value={passwordInput}
            onChange={(e) => setPasswordInput(e.target.value)}
            style={{ padding: "8px", marginRight: "10px" }}
          />
          <button onClick={verifyPassword} style={{ background: "#fffc00", color: "black", padding: "8px", border: "none" }}>
            Login
          </button>
          <button onClick={() => setIsVerifying(false)} style={{ marginLeft: "10px", padding: "8px" }}>
            Abbrechen
          </button>
        </div>
      ) : (
        <button
          onClick={() => setIsVerifying(true)}
          style={{ marginBottom: 20, background: "#fffc00", color: "black", padding: "8px", border: "none" }}
        >
          Admin-Modus aktivieren
        </button>
      )}

      <input
        type="text"
        placeholder="Suche..."
        style={{ padding: "8px", background: "black", color: "#00f0ff", border: "1px solid #00f0ff", marginBottom: "20px" }}
        value={searchQuery}
        onChange={(e) => setSearchQuery(e.target.value)}
      />

      {isAdmin && (
        <button onClick={handleAddProduct} style={{ marginLeft: "10px", background: "#00f0ff", color: "black", padding: "8px", border: "none" }}>
          + Produkt hinzufügen
        </button>
      )}

      <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit, minmax(200px, 1fr))", gap: "20px", marginTop: "30px" }}>
        {filteredProducts.map((product, i) => (
          <div key={product.id} style={{ border: "1px solid #00f0ff", padding: "20px", borderRadius: "10px", background: "#111" }}>
            {isAdmin ? (
              <>
                <input
                  type="text"
                  value={product.name}
                  onChange={(e) => handleProductChange(i, "name", e.target.value)}
                  style={{ width: "100%", marginBottom: "5px" }}
                />
                <textarea
                  value={product.description}
                  onChange={(e) => handleProductChange(i, "description", e.target.value)}
                  style={{ width: "100%", marginBottom: "5px" }}
                ></textarea>
                <input
                  type="number"
                  value={product.price}
                  onChange={(e) => handleProductChange(i, "price", e.target.value)}
                  style={{ width: "100%", marginBottom: "5px" }}
                />
                <button onClick={() => handleDeleteProduct(i)} style={{ background: "red", color: "white", border: "none", padding: "5px" }}>
                  Löschen
                </button>
              </>
            ) : (
              <>
                <h2 style={{ color: "#fffc00" }}>{product.name}</h2>
                <p>{product.description}</p>
                <p>Preis: {product.price} €</p>
                <button
                  style={{ background: "#fffc00", color: "black", padding: "10px", marginTop: "10px", border: "none", cursor: "pointer" }}
                  onClick={() => {
                    handleAddToCart(product);
                    handleOpenOrderForm(product);
                  }}
                >
                  Kaufen
                </button>
              </>
            )}
          </div>
        ))}
      </div>

      {showForm && (
        <div style={{ position: "fixed", top: 0, left: 0, right: 0, bottom: 0, background: "rgba(0,0,0,0.9)", display: "flex", justifyContent: "center", alignItems: "center" }}>
          <form onSubmit={handleSubmit} style={{ background: "#222", padding: "30px", borderRadius: "10px", border: "1px solid #00f0ff", color: "white", width: "300px" }}>
            <h3 style={{ marginBottom: "20px" }}>Bestellung: {selectedProduct.name}</h3>
            <input
              type="text"
              placeholder="Dein Name"
              value={formData.name}
              onChange={(e) => setFormData({ ...formData, name: e.target.value })}
              required
              style={{ width: "100%", padding: "8px", marginBottom: "10px", background: "black", color: "white", border: "1px solid #00f0ff" }}
            />
            <input
              type="email"
              placeholder="Deine E-Mail"
              value={formData.email}
              onChange={(e) => setFormData({ ...formData, email: e.target.value })}
              required
              style={{ width: "100%", padding: "8px", marginBottom: "10px", background: "black", color: "white", border: "1px solid #00f0ff" }}
            />
            <textarea
              placeholder="Nachricht (optional)"
              value={formData.message}
              onChange={(e) => setFormData({ ...formData, message: e.target.value })}
              style={{ width: "100%", padding: "8px", marginBottom: "10px", background: "black", color: "white", border: "1px solid #00f0ff" }}
            ></textarea>
            {errorMessage && <div style={{ color: "red", marginBottom: "10px" }}>{errorMessage}</div>}
            <button type="submit" style={{ background: "#fffc00", color: "black", padding: "10px", width: "100%", border: "none" }}>
              Bestellung absenden
            </button>
            <button type="button" onClick={() => setShowForm(false)} style={{ marginTop: "10px", color: "#00f0ff", background: "transparent", border: "none", cursor: "pointer" }}>
              Abbrechen
            </button>
          </form>
        </div>
      )}

      {successMessage && <div style={{ color: "lime", marginTop: "20px" }}>{successMessage}</div>}

      <footer style={{ marginTop: "50px", borderTop: "1px solid #00f0ff", paddingTop: "20px", textAlign: "center", color: "#00f0ff" }}>
        <p>Support & Hilfe: <a href="mailto:info@noirwear.de" style={{ color: "#fffc00" }}>info@noirwear.de</a></p>
        <p>© {new Date().getFullYear()} NOIRWEAR. Alle Rechte vorbehalten.</p>
      </footer>
    </div>
  );
}
