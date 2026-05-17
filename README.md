🎬 Movies Recommendation System 🎥

This project is a Movie Recommendation System built using Machine Learning, Python, and Streamlit. The system suggests movies based on content-based filtering and cosine similarity. The web app fetches movie posters, descriptions, ratings, and trailers using the TMDB API to provide a rich user experience.


---

📌 Features

✅ Personalized Movie Recommendations based on similarity.
✅ Movie Posters, Trailers & Details using TMDB API.
✅ User-friendly Web Interface powered by Streamlit.
✅ Precomputed Similarity Matrix for fast recommendations.
✅ Scalable & Efficient model using preprocessed data.


---

📂 Project Structure

├── app.py                          # Web application using Streamlit
├── MoviesRecommendationSystem.ipynb  # Data preprocessing and model building
├── movie_dict.pkl                   # Precomputed movie metadata dictionary
├── similarity.pkl                    # Precomputed similarity matrix
├── README.md                        # Documentation


---

🚀 How It Works

The recommendation system consists of two main components:

1. Data Preprocessing & Model Training 📊 (Jupyter Notebook)


2. Web App Deployment 🎭 (Streamlit)




---

🔹 1️⃣ Data Preprocessing & Model Training (Jupyter Notebook)

This step is responsible for data cleaning, transformation, and computing similarity scores.

🔸 Step 1: Loading Datasets

The system uses two datasets:

tmdb_5000_movies.csv → Contains movie metadata.

tmdb_5000_credits.csv → Contains cast and crew details.

import numpy as np
import pandas as pd

movies = pd.read_csv('tmdb_5000_movies.csv')
credits = pd.read_csv('tmdb_5000_credits.csv')

The datasets are merged using the common column title.

movies = movies.merge(credits, on='title')


---

🔸 Step 2: Data Cleaning & Feature Selection

Only relevant columns are selected:

movies = movies[['movie_id', 'title', 'genres', 'overview', 'cast', 'crew', 'keywords']]

🟢 Handling Missing Data

movies.dropna(inplace=True)

🟢 Converting JSON-like Strings to Python Lists

The genres, cast, crew, and keywords columns contain JSON-like lists. These are converted to proper Python lists:

import ast

def convert(obj):
L = []
for i in ast.literal_eval(obj):
L.append(i['name'])
return L

movies['genres'] = movies['genres'].apply(convert)
movies['keywords'] = movies['keywords'].apply(convert)


---

🔸 Step 3: Extracting Key Information

🟢 Selecting the Top 3 Cast Members

def convert3(obj):
L = []
counter = 0
for i in ast.literal_eval(obj):
if counter != 3:
L.append(i['name'])
counter += 1
else:
break
return L

movies['cast'] = movies['cast'].apply(convert3)

🟢 Extracting Director Name

def fetch_director(obj):
L = []
for i in ast.literal_eval(obj):
if i['job'] == 'Director':
L.append(i['name'])
break
return L

movies['crew'] = movies['crew'].apply(fetch_director)


---

🔸 Step 4: Creating a "Tags" Column

A new column tags is created by combining overview, genres, keywords, cast, and crew.

movies['tags'] = movies['overview'] + movies['genres'] + movies['keywords'] + movies['cast'] + movies['crew']

All tags are converted into a single string for processing:

movies['tags'] = movies['tags'].apply(lambda x: " ".join(x))


---

🔸 Step 5: Text Vectorization (TF-IDF)

The TF-IDF (Term Frequency - Inverse Document Frequency) technique converts text into numerical feature vectors.

from sklearn.feature_extraction.text import TfidfVectorizer

tfidf = TfidfVectorizer(max_features=5000, stop_words='english')
vectorized_matrix = tfidf.fit_transform(movies['tags']).toarray()


---

🔸 Step 6: Computing Cosine Similarity

We calculate the cosine similarity between all movies based on their tags.

from sklearn.metrics.pairwise import cosine_similarity

similarity = cosine_similarity(vectorized_matrix)


---

🔸 Step 7: Saving Processed Data

The movie dictionary and similarity matrix are saved for fast retrieval in the web app.

import pickle

pickle.dump(movies.to_dict(), open('movie_dict.pkl', 'wb'))
pickle.dump(similarity, open('similarity.pkl', 'wb'))


---

🔹 2️⃣ Web App Deployment (Streamlit - app.py)

The web app allows users to select a movie and get recommendations.

🔸 Step 1: Load Precomputed Data

import streamlit as st
import pickle
import pandas as pd
import requests

movies_dict = pickle.load(open('movie_dict.pkl', 'rb'))
movies = pd.DataFrame(movies_dict)
similarity = pickle.load(open('similarity.pkl', 'rb'))


---

🔸 Step 2: Fetching Movie Details from TMDB API

The TMDB API fetches movie posters and trailers.

TMDB_API_KEY = 'YOUR_TMDB_API_KEY'

def fetch_poster(movie_id):
response = requests.get(f'https://api.themoviedb.org/3/movie/{movie_id}?api_key={TMDB_API_KEY}&language=en-US')
data = response.json()
return "https://image.tmdb.org/t/p/w500" + data['poster_path']


---

🔸 Step 3: Movie Recommendation Function

The function finds top 5 similar movies using the precomputed similarity matrix.

def recommend(movie):
index = movies[movies['title'] == movie].index[0]
distances = similarity[index]
movie_list = sorted(list(enumerate(distances)), reverse=True, key=lambda x: x[1])[1:6]

recommended_movies = []    
for i in movie_list:    
    recommended_movies.append(movies.iloc[i[0]].title)    
return recommended_movies


---

🔸 Step 4: Web UI with Streamlit

st.title("🎬 Movies Recommendation System 🎥")

selected_movie = st.selectbox("Choose a movie", movies['title'].values)

if st.button('Recommend'):
recommendations = recommend(selected_movie)
for movie in recommendations:
st.write(movie)


---

🛠 Installation & Setup

 Install Dependencies

pip install -r requirements.txt

 Run the Web App

streamlit run app.py
"

Paste all details that's I give code emoji etc.. "🎬 Movies Recommendation System 🎥

This project is a Movie Recommendation System built using Machine Learning, Python, and Streamlit. The system suggests movies based on content-based filtering and cosine similarity. The web app fetches movie posters, descriptions, ratings, and trailers using the TMDB API to provide a rich user experience.


---

📌 Features

✅ Personalized Movie Recommendations based on similarity.
✅ Movie Posters, Trailers & Details using TMDB API.
✅ User-friendly Web Interface powered by Streamlit.
✅ Precomputed Similarity Matrix for fast recommendations.
✅ Scalable & Efficient model using preprocessed data.


---

📂 Project Structure

├── app.py                          # Web application using Streamlit
├── MoviesRecommendationSystem.ipynb  # Data preprocessing and model building
├── movie_dict.pkl                   # Precomputed movie metadata dictionary
├── similarity.pkl                    # Precomputed similarity matrix
├── README.md                        # Documentation


---

🚀 How It Works

The recommendation system consists of two main components:

1. Data Preprocessing & Model Training 📊 (Jupyter Notebook)


2. Web App Deployment 🎭 (Streamlit)




---

🔹 1️⃣ Data Preprocessing & Model Training (Jupyter Notebook)

This step is responsible for data cleaning, transformation, and computing similarity scores.

🔸 Step 1: Loading Datasets

The system uses two datasets:

tmdb_5000_movies.csv → Contains movie metadata.

tmdb_5000_credits.csv → Contains cast and crew details.

import numpy as np
import pandas as pd

movies = pd.read_csv('tmdb_5000_movies.csv')
credits = pd.read_csv('tmdb_5000_credits.csv')

The datasets are merged using the common column title.

movies = movies.merge(credits, on='title')


---

🔸 Step 2: Data Cleaning & Feature Selection

Only relevant columns are selected:

movies = movies[['movie_id', 'title', 'genres', 'overview', 'cast', 'crew', 'keywords']]

🟢 Handling Missing Data

movies.dropna(inplace=True)

🟢 Converting JSON-like Strings to Python Lists

The genres, cast, crew, and keywords columns contain JSON-like lists. These are converted to proper Python lists:

import ast

def convert(obj):
L = []
for i in ast.literal_eval(obj):
L.append(i['name'])
return L

movies['genres'] = movies['genres'].apply(convert)
movies['keywords'] = movies['keywords'].apply(convert)


---

🔸 Step 3: Extracting Key Information

🟢 Selecting the Top 3 Cast Members

def convert3(obj):
L = []
counter = 0
for i in ast.literal_eval(obj):
if counter != 3:
L.append(i['name'])
counter += 1
else:
break
return L

movies['cast'] = movies['cast'].apply(convert3)

🟢 Extracting Director Name

def fetch_director(obj):
L = []
for i in ast.literal_eval(obj):
if i['job'] == 'Director':
L.append(i['name'])
break
return L

movies['crew'] = movies['crew'].apply(fetch_director)


---

🔸 Step 4: Creating a "Tags" Column

A new column tags is created by combining overview, genres, keywords, cast, and crew.

movies['tags'] = movies['overview'] + movies['genres'] + movies['keywords'] + movies['cast'] + movies['crew']

All tags are converted into a single string for processing:

movies['tags'] = movies['tags'].apply(lambda x: " ".join(x))


---

🔸 Step 5: Text Vectorization (TF-IDF)

The TF-IDF (Term Frequency - Inverse Document Frequency) technique converts text into numerical feature vectors.

from sklearn.feature_extraction.text import TfidfVectorizer

tfidf = TfidfVectorizer(max_features=5000, stop_words='english')
vectorized_matrix = tfidf.fit_transform(movies['tags']).toarray()


---

🔸 Step 6: Computing Cosine Similarity

We calculate the cosine similarity between all movies based on their tags.

from sklearn.metrics.pairwise import cosine_similarity

similarity = cosine_similarity(vectorized_matrix)


---

🔸 Step 7: Saving Processed Data

The movie dictionary and similarity matrix are saved for fast retrieval in the web app.

import pickle

pickle.dump(movies.to_dict(), open('movie_dict.pkl', 'wb'))
pickle.dump(similarity, open('similarity.pkl', 'wb'))


---

🔹 2️⃣ Web App Deployment (Streamlit - app.py)

The web app allows users to select a movie and get recommendations.

🔸 Step 1: Load Precomputed Data

import streamlit as st
import pickle
import pandas as pd
import requests

movies_dict = pickle.load(open('movie_dict.pkl', 'rb'))
movies = pd.DataFrame(movies_dict)
similarity = pickle.load(open('similarity.pkl', 'rb'))


---

🔸 Step 2: Fetching Movie Details from TMDB API

The TMDB API fetches movie posters and trailers.

TMDB_API_KEY = 'YOUR_TMDB_API_KEY'

def fetch_poster(movie_id):
response = requests.get(f'https://api.themoviedb.org/3/movie/{movie_id}?api_key={TMDB_API_KEY}&language=en-US')
data = response.json()
return "https://image.tmdb.org/t/p/w500" + data['poster_path']


---

🔸 Step 3: Movie Recommendation Function

The function finds top 5 similar movies using the precomputed similarity matrix.

def recommend(movie):
index = movies[movies['title'] == movie].index[0]
distances = similarity[index]
movie_list = sorted(list(enumerate(distances)), reverse=True, key=lambda x: x[1])[1:6]

recommended_movies = []    
for i in movie_list:    
    recommended_movies.append(movies.iloc[i[0]].title)    
return recommended_movies


---

🔸 Step 4: Web UI with Streamlit

st.title("🎬 Movies Recommendation System 🎥")

selected_movie = st.selectbox("Choose a movie", movies['title'].values)

if st.button('Recommend'):
recommendations = recommend(selected_movie)
for movie in recommendations:
st.write(movie)


---


