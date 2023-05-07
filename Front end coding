Navigation Bar
import React from 'react';
import { Link } from 'react-router-dom';

function NavigationBar() {
  return (
    <nav>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/dashboard">Dashboard</Link>
        </li>
        <li>
          <Link to="/prediction-form">Prediction Form</Link>
        </li>
        <li>
          <Link to="/news-feed">News Feed</Link>
        </li>
        <li>
          <Link to="/user-account">User Account</Link>
        </li>
      </ul>
    </nav>
  );
}

export default NavigationBar;


#Dashboard component for Stock Price market and prediction.

import React, { useState, useEffect } from 'react';
import axios from 'axios';
import Chart from 'chart.js';

function Dashboard() {
  const [stockData, setStockData] = useState(null);

  useEffect(() => {
    axios.get('/api/stock-data')
      .then(response => {
        setStockData(response.data);
        createChart(response.data);
      })
      .catch(error => {
        console.log(error);
      });
  }, []);

  function createChart(data) {
    const ctx = document.getElementById('stock-chart');
    const labels = data.map(item => item.date);
    const prices = data.map(item => item.price);

    new Chart(ctx, {
      type: 'line',
      data: {
        labels: labels,
        datasets: [{
          label: 'Stock Price',
          data: prices,
          backgroundColor: 'rgba(54, 162, 235, 0.2)',
          borderColor: 'rgba(54, 162, 235, 1)',
          borderWidth: 1
        }]
      },
      options: {
        scales: {
          yAxes: [{
            ticks: {
              beginAtZero: true
            }
          }]
        }
      }
    });
  }

  return (
    <div>
      <h2>Stock Market Data</h2>
      {stockData ? (
        <div>
          <canvas id="stock-chart"></canvas>
        </div>
      ) : (
        <p>Loading data...</p>
      )}
    </div>
  );
}

export default Dashboard;


#Prediction Form
import React, { useState } from 'react';

function PredictionForm() {
  const [stockSymbol, setStockSymbol] = useState('');
  const [startDate, setStartDate] = useState('');
  const [endDate, setEndDate] = useState('');
  const [predictions, setPredictions] = useState([]);

  const handleSubmit = async (event) => {
    event.preventDefault();

    const response = await fetch(`/api/predictions?symbol=${stockSymbol}&start_date=${startDate}&end_date=${endDate}`);
    const data = await response.json();
    setPredictions(data.predictions);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Stock Symbol:
        <input type="text" value={stockSymbol} onChange={(event) => setStockSymbol(event.target.value)} />
      </label>
      <label>
        Start Date:
        <input type="date" value={startDate} onChange={(event) => setStartDate(event.target.value)} />
      </label>
      <label>
        End Date:
        <input type="date" value={endDate} onChange={(event) => setEndDate(event.target.value)} />
      </label>
      <button type="submit">Predict</button>

      <h2>Predictions</h2>
      {predictions.length > 0 ? (
        <ul>
          {predictions.map((prediction, index) => (
            <li key={index}>
              Date: {prediction.date}, Price: {prediction.price}
            </li>
          ))}
        </ul>
      ) : (
        <p>No predictions yet</p>
      )}
    </form>
  );
}

export default PredictionForm;


#News feed
import React, { useEffect, useState } from 'react';

function NewsFeed() {
  const [news, setNews] = useState([]);

  useEffect(() => {
    const fetchNews = async () => {
      const response = await fetch('/api/news');
      const data = await response.json();
      setNews(data.articles);
    };

    fetchNews();
  }, []);

  return (
    <div>
      <h2>News Feed</h2>
      <ul>
        {news.map((article, index) => (
          <li key={index}>
            <a href={article.url} target="_blank" rel="noopener noreferrer">
              <img src={article.urlToImage} alt={article.title} />
              <h3>{article.title}</h3>
            </a>
            <p>{article.description}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default NewsFeed;

#User Account
import React, { useState, useEffect } from 'react';

function UserAccount() {
  const [user, setUser] = useState({});
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        const response = await fetch('/api/user');
        const data = await response.json();
        setUser(data.user);
        setIsLoading(false);
      } catch (error) {
        setIsLoading(false);
        setError(error.message);
      }
    };

    fetchUser();
  }, []);

  const handleUpdateProfile = async (event) => {
    event.preventDefault();

    // Code to update user profile using API
    // ...

    // Update the user state with the new profile data
    setUser(updatedUserData);
  };

  return (
    <div>
      <h2>User Account</h2>
      {isLoading ? (
        <p>Loading...</p>
      ) : error ? (
        <p>{error}</p>
      ) : (
        <>
          <p>Account ID: {user.id}</p>
          <p>Name: {user.name}</p>
          <p>Email: {user.email}</p>

          <h3>Portfolio</h3>
          <ul>
            {user.portfolio.map((item, index) => (
              <li key={index}>
                {item.name}: {item.quantity}
              </li>
            ))}
          </ul>

          <h3>Transaction History</h3>
          <ul>
            {user.transactionHistory.map((transaction, index) => (
              <li key={index}>
                Date: {transaction.date}, Type: {transaction.type}, Amount: {transaction.amount}
              </li>
            ))}
          </ul>

          <form onSubmit={handleUpdateProfile}>
            <label>
              Name:
              <input type="text" value={user.name} onChange={(event) => setUser({ ...user, name: event.target.value })} />
            </label>
            <label>
              Email:
              <input type="email" value={user.email} onChange={(event) => setUser({ ...user, email: event.target.value })} />
            </label>
            <button type="submit">Update Profile</button>
          </form>
        </>
      )}
    </div>
  );
}

export default UserAccount;


#authentication and autherization
import React, { useState } from 'react';
import { Redirect, Route, Switch } from 'react-router-dom';
import LoginForm from './LoginForm';
import Dashboard from './Dashboard';
import ProtectedRoute from './ProtectedRoute';

function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [userRole, setUserRole] = useState('');

  const handleLogin = (username, password) => {
    // Code to authenticate user using API
    // ...

    // Set the authenticated state to true if the user is valid
    setIsAuthenticated(true);

    // Set the user role based on the user data returned from the API
    setUserRole(userData.role);
  };

  const handleLogout = () => {
    // Code to log out user using API
    // ...

    // Set the authenticated state to false
    setIsAuthenticated(false);

    // Clear the user role
    setUserRole('');
  };

  return (
    <div>
      <Switch>
        <Route exact path="/login">
          <LoginForm onLogin={handleLogin} />
        </Route>

        <ProtectedRoute exact path="/dashboard" isAuthenticated={isAuthenticated} userRole={userRole}>
          <Dashboard onLogout={handleLogout} />
        </ProtectedRoute>

        <Redirect to="/dashboard" />
      </Switch>
    </div>
  );
}

export default App;


# Mobile responsiveness

import React, { useState } from 'react';
import './App.css';

function App() {
  const [isMenuOpen, setIsMenuOpen] = useState(false);

  const handleMenuClick = () => {
    setIsMenuOpen(!isMenuOpen);
  };

  return (
    <div className="App">
      <header>
        <h1>Stock Market and Prediction</h1>
        <button onClick={handleMenuClick}>{isMenuOpen ? 'Close' : 'Menu'}</button>
      </header>

      <nav className={isMenuOpen ? 'open' : ''}>
        <ul>
          <li>Home</li>
          <li>About</li>
          <li>Contact</li>
        </ul>
      </nav>

      <main>
        <p>Welcome to our app!</p>
        <p>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
        </p>
      </main>

      <footer>
        <p>&copy; 2023 Stock Market and Prediction</p>
      </footer>
    </div>
  );
}

export default App;
.css

@media screen and (max-width: 600px) {
  nav ul {
    display: none;
    flex-direction: column;
    margin: 0;
    padding: 0;
    position: absolute;
    top: 60px;
    left: 0;
    right: 0;
    background-color: #f5f5f5;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
    z-index: 1;
  }

  nav.open ul {
    display: flex;
  }

  nav ul li {
    margin: 0;
    padding: 10px;
    border-bottom: 1px solid #ccc;
  }

  header button {
    display: block;
    margin-left: auto;
    background-color: transparent;
    border: none;
    font-size: 20px;
  }

  header button:focus {
    outline: none;
  }
}
