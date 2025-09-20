# btthuchanh
import React, { useState } from "react";

// ProfileEditor.jsx
// Single-file React component to edit/update a user's personal profile.
// TailwindCSS classes are used for styling (no imports required here).
// Features:
// - Avatar upload with preview
// - Fields: Full name, email, role, bio, location, social links
// - Simple client-side validation
// - Mock save function (replace with your API call)

export default function ProfileEditor() {
  const [form, setForm] = useState({
    fullName: "",
    email: "",
    role: "",
    location: "",
    bio: "",
    twitter: "",
    linkedin: "",
  });
  const [avatar, setAvatar] = useState(null); // File
  const [avatarPreview, setAvatarPreview] = useState(null); // Data URL
  const [saving, setSaving] = useState(false);
  const [errors, setErrors] = useState({});
  const [message, setMessage] = useState(null);

  function handleChange(e) {
    const { name, value } = e.target;
    setForm((s) => ({ ...s, [name]: value }));
  }

  function handleAvatar(e) {
    const file = e.target.files?.[0] ?? null;
    setAvatar(file);
    if (!file) {
      setAvatarPreview(null);
      return;
    }
    const reader = new FileReader();
    reader.onload = (ev) => setAvatarPreview(ev.target.result);
    reader.readAsDataURL(file);
  }

  function validate() {
    const errs = {};
    if (!form.fullName.trim()) errs.fullName = "Vui lòng nhập họ tên.";
    if (!form.email.trim()) errs.email = "Vui lòng nhập email.";
    else if (!/^\S+@\S+\.\S+$/.test(form.email)) errs.email = "Email không hợp lệ.";
    if (form.bio.length > 500) errs.bio = "Tiểu sử không được vượt quá 500 ký tự.";
    return errs;
  }

  async function handleSave(e) {
    e.preventDefault();
    setMessage(null);
    const errs = validate();
    setErrors(errs);
    if (Object.keys(errs).length) return;

    // mock save
    setSaving(true);
    try {
      // prepare payload
      const payload = new FormData();
      payload.append("fullName", form.fullName);
      payload.append("email", form.email);
      payload.append("role", form.role);
      payload.append("location", form.location);
      payload.append("bio", form.bio);
      payload.append("twitter", form.twitter);
      payload.append("linkedin", form.linkedin);
      if (avatar) payload.append("avatar", avatar);

      // Replace the following with your real API call, e.g.:
      // await fetch('/api/profile', { method: 'POST', body: payload });
      await new Promise((res) => setTimeout(res, 900)); // simulate network

      setMessage({ type: "success", text: "Cập nhật hồ sơ thành công." });
    } catch (err) {
      setMessage({ type: "error", text: "Lỗi khi lưu. Vui lòng thử lại." });
    } finally {
      setSaving(false);
    }
  }

  return (
    <div className="max-w-3xl mx-auto p-6 bg-white rounded-2xl shadow-md">
      <h2 className="text-2xl font-semibold mb-4">Cập nhật hồ sơ cá nhân</h2>

      {message && (
        <div
          className={`mb-4 p-3 rounded-md text-sm ${
            message.type === "success" ? "bg-green-50 text-green-700" : "bg-red-50 text-red-700"
          }`}
        >
          {message.text}
        </div>
      )}

      <form onSubmit={handleSave}>
        <div className="flex gap-6 items-start">
          <div className="w-32 h-32 rounded-xl overflow-hidden bg-gray-100 flex items-center justify-center">
            {avatarPreview ? (
              <img src={avatarPreview} alt="avatar" className="w-full h-full object-cover" />
            ) : (
              <div className="text-center text-sm text-gray-500">Ảnh đại diện</div>
            )}
          </div>

          <div className="flex-1">
            <label className="block text-sm font-medium text-gray-700">Ảnh đại diện</label>
            <input type="file" accept="image/*" onChange={handleAvatar} className="mt-2" />
            <p className="text-xs text-gray-500 mt-1">jpg, png. Kích thước tối ưu 400x400.</p>
          </div>
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mt-6">
          <div>
            <label className="block text-sm font-medium text-gray-700">Họ và tên</label>
            <input
              name="fullName"
              value={form.fullName}
              onChange={handleChange}
              className={`mt-1 block w-full rounded-lg border px-3 py-2 focus:outline-none focus:ring ${
                errors.fullName ? "border-red-400" : "border-gray-200"
              }`}
              placeholder="Ví dụ: Nguyễn Văn A"
            />
            {errors.fullName && <p className="text-red-600 text-xs mt-1">{errors.fullName}</p>}
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700">Email</label>
            <input
              name="email"
              type="email"
              value={form.email}
              onChange={handleChange}
              className={`mt-1 block w-full rounded-lg border px-3 py-2 focus:outline-none focus:ring ${
                errors.email ? "border-red-400" : "border-gray-200"
              }`}
              placeholder="email@domain.com"
            />
            {errors.email && <p className="text-red-600 text-xs mt-1">{errors.email}</p>}
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700">Chức danh / Vai trò</label>
            <input
              name="role"
              value={form.role}
              onChange={handleChange}
              className="mt-1 block w-full rounded-lg border border-gray-200 px-3 py-2 focus:outline-none focus:ring"
              placeholder="Ví dụ: Diễn viên / Biên kịch / Developer"
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700">Địa điểm</label>
            <input
              name="location"
              value={form.location}
              onChange={handleChange}
              className="mt-1 block w-full rounded-lg border border-gray-200 px-3 py-2 focus:outline-none focus:ring"
              placeholder="Thành phố, Quốc gia"
            />
          </div>
        </div>

        <div className="mt-4">
          <label className="block text-sm font-medium text-gray-700">Tiểu sử (bio)</label>
          <textarea
            name="bio"
            value={form.bio}
            onChange={handleChange}
            rows={4}
            className={`mt-1 block w-full rounded-lg border px-3 py-2 focus:outline-none focus:ring ${
              errors.bio ? "border-red-400" : "border-gray-200"
            }`}
            placeholder="Viết vài dòng giới thiệu về bản thân..."
          />
          <div className="text-xs text-gray-500 mt-1">{form.bio.length}/500 ký tự</div>
          {errors.bio && <p className="text-red-600 text-xs mt-1">{errors.bio}</p>}
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
          <div>
            <label className="block text-sm font-medium text-gray-700">Twitter</label>
            <input
              name="twitter"
              value={form.twitter}
              onChange={handleChange}
              className="mt-1 block w-full rounded-lg border border-gray-200 px-3 py-2 focus:outline-none focus:ring"
              placeholder="@username"
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700">LinkedIn</label>
            <input
              name="linkedin"
              value={form.linkedin}
              onChange={handleChange}
              className="mt-1 block w-full rounded-lg border border-gray-200 px-3 py-2 focus:outline-none focus:ring"
              placeholder="linkedin.com/in/your-name"
            />
          </div>
        </div>

        <div className="flex items-center gap-3 mt-6">
          <button
            type="submit"
            disabled={saving}
            className="px-4 py-2 rounded-lg bg-blue-600 text-white font-medium hover:opacity-95 disabled:opacity-60"
          >
            {saving ? "Đang lưu..." : "Lưu thay đổi"}
          </button>

          <button
            type="button"
            onClick={() => {
              // simple reset
              setForm({ fullName: "", email: "", role: "", location: "", bio: "", twitter: "", linkedin: "" });
              setAvatar(null);
              setAvatarPreview(null);
              setErrors({});
              setMessage(null);
            }}
            className="px-3 py-2 rounded-lg border border-gray-200 bg-white text-sm"
          >
            Đặt lại
          </button>
        </div>
      </form>

      <div className="mt-6 text-xs text-gray-500">
        Gợi ý: Thay quyền gọi API trong <code>/api/profile</code> bằng endpoint thực tế của bạn.
      </div>
    </div>
  );
}
