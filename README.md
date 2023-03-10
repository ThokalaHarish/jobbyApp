#jobbyApp LoginFrom index.js

import {Component} from 'react'
import Cookies from 'js-cookie'
import {Redirect} from 'react-router-dom'

import './index.css'

class LoginForm extends Component {
  state = {
    username: '',
    password: '',
    showSubmitError: false,
    errorMsg: '',
  }

  onChangeUserName = event => {
    this.setState({username: event.target.value})
  }

  onChangePassword = event => {
    this.setState({password: event.target.value})
  }

  onSubmitSuccess = jwtToken => {
    const {history} = this.props

    Cookies.set('jobby_jwt_token', jwtToken, {expires: 30, path: '/'})
    history.replace('/')
  }

  onSubmitFailure = errorMsg => {
    this.setState({showSubmitError: ture, errorMsg})
  }

  submitForm = async event => {
    event.preventDefault()
    const {username, password} = this.state
    const userDetails = {username, password}
    const url = 'https://apis.ccbp.in/login'

    const options = {
      method: 'POST',
      body: JSON.stringify(userDetails),
    }

    const response = await fetch(url, options)
    const data = await response.json()
    if (response.ok === true) {
      this.onSubmitSuccess(data.jwt_token)
    } else {
      this.onSubmitFailure(data.error_msg)
    }
  }

  renderPassword = () => {
    const {password} = this.state
    return (
      <>
        <label htmlFor="password" className="input-label">
          PASSWORD
        </label>
        <input
          type="password"
          className="input-field"
          id="password"
          placeholder="Password"
          value={password}
          onChange={this.onChangePassword}
        />
      </>
    )
  }

  renderUserName = () => {
    const {username} = this.state
    return (
      <>
        <label htmlFor="username" className="input-label">
          USERName
        </label>
        <input
          type="text"
          id="username"
          className="input-field"
          value={username}
          placeholder="Username"
          onChange={this.onChangeUserName}
        />
      </>
    )
  }

  render() {
    const {errorMsg, showSubmitError} = this.state
    const jwtToken = Cookies.get('jobby_jwt_token')
    if (jwtToken !== undefined) {
      return <Redirect to="/" />
    }

    return (
      <div className="login-form-container">
        <form className="for-container" onSubmit={this.submitForm}>
          <img
            src="https://assets.ccbp.in/frontend/react-js/logo-img.png"
            alt="website logo"
            className="website-logo"
          />
          <div className="input-container">{this.renderUserName()}</div>
          <div className="input-container">{this.renderPassword()}</div>
          <button className="login-button" type="submit">
            Login
          </button>
          {showSubmitError && <p className="error-msg">*{errorMsg}</p>}
        </form>
      </div>
    )
  }
}

export default LoginForm



