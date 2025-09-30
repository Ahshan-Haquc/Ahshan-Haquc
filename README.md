import { useEffect, useState } from "react";
import { NavLink, useNavigate } from "react-router-dom";
import { useAuthUser } from "../context/AuthContext";
import toastShow from "../utils/toastShow";
import { Mail, Lock, LogIn, FileText } from "lucide-react"; // FileText icon for branding

const Login = () => {
  const [formUser, setFormUser] = useState({ email: "", password: "" });
  const navigate = useNavigate();
  const { user, setUser } = useAuthUser();

  useEffect(() => {
    document.title = "Login - ProFileGen";
    toastShow("You don't have to verify your email, so don't use your actual email and password if you're concerned about security.");
  }, []);

  const handleInput = (e) => {
    const { name, value } = e.target;
    setFormUser({ ...formUser, [name]: value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const response = await fetch(`${import.meta.env.VITE_API_URL}/userLogin`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        credentials: "include",
        body: JSON.stringify({
          email: formUser.email,
          password: formUser.password,
        }),
      });

      const data = await response.json();
      if (response.ok) {
        setUser(data.user);
        if (data.user.role === "admin") {
          navigate("/adminDashboard");
        } else {
          navigate("/cvDashboard");
        }
      } else {
        toastShow("Login unsuccessful. Please check your credentials.", "error");
      }
    } catch (err) {
      console.error("Login error:", err);
      toastShow("Login failed due to a network error. Please try again.", "error");
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center p-4 sm:p-6 font-sans bg-gradient-to-br from-purple-500 to-indigo-600 relative overflow-hidden">
      {/* Background circles for visual effect */}
      <div className="absolute w-64 h-64 bg-purple-400 rounded-full mix-blend-multiply filter blur-2xl opacity-60 animate-blob top-1/4 left-1/4 transform -translate-x-1/2 -translate-y-1/2"></div>
      <div className="absolute w-64 h-64 bg-indigo-400 rounded-full mix-blend-multiply filter blur-2xl opacity-60 animate-blob top-3/4 left-3/4 transform -translate-x-1/2 -translate-y-1/2 animation-delay-2000"></div>

      {/* Login Card */}
      <div className="w-full max-w-sm bg-white/20 backdrop-blur-lg rounded-2xl shadow-xl border border-white/30 p-8 sm:p-10 relative z-10">
        
        {/* Header Section */}
        <div className="text-center mb-8">
          <div className="flex justify-center mb-4">
            <FileText size={48} className="text-white drop-shadow-lg" />
          </div>
          <h1 className="text-4xl font-bold text-white tracking-tight drop-shadow-md">
            ProFile<span className="font-light">Gen</span>
          </h1>
          <p className="mt-2 text-white/90 text-sm">
            Log in to manage your professional identity.
          </p>
        </div>

        {/* Login Form */}
        <form onSubmit={handleSubmit} className="space-y-6">
          {/* Email Input */}
          <div className="relative">
            <Mail className="absolute left-3 top-1/2 transform -translate-y-1/2 text-white/60" size={20} />
            <input
              type="email"
              placeholder="Email address"
              name="email"
              value={formUser.email}
              onChange={handleInput}
              className="w-full pl-10 pr-3 py-3 bg-white/10 text-white placeholder-white/60 border border-white/30 rounded-lg focus:outline-none focus:ring-2 focus:ring-white transition-all duration-200"
              required
            />
          </div>

          {/* Password Input */}
          <div className="relative">
            <Lock className="absolute left-3 top-1/2 transform -translate-y-1/2 text-white/60" size={20} />
            <input
              type="password"
              placeholder="Password"
              name="password"
              value={formUser.password}
              onChange={handleInput}
              className="w-full pl-10 pr-3 py-3 bg-white/10 text-white placeholder-white/60 border border-white/30 rounded-lg focus:outline-none focus:ring-2 focus:ring-white transition-all duration-200"
              required
            />
          </div>

          {/* Forgot Password */}
          <div className="text-right text-sm">
            <NavLink to="/forgot-password" className="text-white/80 hover:text-white font-medium transition-colors">
              Forgot password?
            </NavLink>
          </div>

          {/* Login Button */}
          <button
            type="submit"
            className="w-full flex items-center justify-center gap-2 py-3 bg-white text-purple-600 font-bold rounded-lg shadow-lg hover:bg-gray-100 transition-colors transform hover:scale-105"
          >
            <LogIn size={20} />
            Log In
          </button>
        </form>

        {/* Signup Link */}
        <div className="mt-6 text-center text-sm text-white/80">
          <p>
            New to ProFileGen?{" "}
            <NavLink to="/signup" className="text-white hover:underline font-semibold">
              Create an account
            </NavLink>
          </p>
        </div>
      </div>
    </div>
  );
};

export default Login;
